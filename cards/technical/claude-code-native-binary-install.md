---
title: claude 跑不起来：npm optional dep + Bun stub + grep 劫持三层叠
tags: claude-code, npm, optional-dependencies, bun-binary, shell-function-override, debugging
created: 2026-05-12
---

# claude 跑不起来：npm optional dep + Bun stub + grep 劫持三层叠

## 症状

`claude` 命令行为分两阶段：
1. 第一次：`zsh: permission denied: claude`
2. `chmod +x` 后：`Error: claude native binary not installed. ... Run the postinstall manually`

同时 session 内 `grep` 也开始报相同的 `claude native binary not installed`，看起来像 zshrc 在调 claude，误导诊断方向。

## 三层根因

### 第 1 层：`.exe` stub 没执行位

```
/opt/homebrew/bin/claude → ../lib/node_modules/@anthropic-ai/claude-code/bin/claude.exe
```

Anthropic 从 v2.x 起把 CLI 改成 Bun-compiled binary，文件名 `claude.exe`（即使在 macOS 上）。npm install 装的时候没设 +x，stub 跑不起来。

### 第 2 层：platform-native package 是 optional dep

`claude.exe` 本身只是 launcher stub，启动时找 `@anthropic-ai/claude-code-<platform>-<arch>`（比如 `darwin-arm64`）拿真正的 native binary。这些 platform package 在 `optionalDependencies` 里：

```json
"optionalDependencies": {
  "@anthropic-ai/claude-code-darwin-arm64": "2.1.128",
  "@anthropic-ai/claude-code-darwin-x64":   "2.1.128",
  "@anthropic-ai/claude-code-linux-x64":    "2.1.128",
  ...
}
```

如果 npm install 时跑了 `--omit=optional` 或者 npm config 设了 `omit=optional`（brew 装的 node 在某些版本下会默认这样），那 platform package 全部跳过 → claude.exe stub 找不到 native binary → 报 `native binary not installed`。

### 第 3 层：`grep` 被 Claude Code session 劫持

```
$ type grep
grep is a shell function from /Users/xiaoszhang/.claude/shell-snapshots/snapshot-zsh-*.sh
grep () {
    local _cc_bin="${CLAUDE_CODE_EXECPATH:-}"
    [[ -x $_cc_bin ]] || _cc_bin=/Users/xiaoszhang/.local/bin/claude
    ...
    exec -a ugrep "$_cc_bin" -G --ignore-files --hidden -I ... "$@"
}
```

Claude Code 在 session shell 里把 `grep` / `find` / 之类 override 成 shell function，调 `claude` 的内置 ugrep（性能优化 + 自动尊重 .gitignore）。所以 claude binary 一坏，`grep` 也跟着挂，且报错文本一样 → 看起来像 zshrc 在调 claude，让人去查 hook / precmd / .local/bin/env，全是死胡同。

## 修法

```bash
# 1. 加执行权限
chmod +x /opt/homebrew/lib/node_modules/@anthropic-ai/claude-code/bin/claude.exe

# 2. 装匹配版本的 native package（看 package.json 里 optionalDependencies 的版本号）
npm install -g @anthropic-ai/claude-code-darwin-arm64@2.1.128

# 3. 跑 postinstall 关联
node /opt/homebrew/lib/node_modules/@anthropic-ai/claude-code/install.cjs

# 验证
claude --version
```

## 诊断手法

1. `type grep` —— 看到 `shell function from ~/.claude/shell-snapshots/...` 就是被劫持了，先别去 grep 找问题
2. `ls -la $(readlink -f $(which claude))` —— 确认 stub 有 +x
3. `ls /opt/homebrew/lib/node_modules/@anthropic-ai/claude-code-darwin-arm64` —— 缺这个目录就是 optional dep 跳过了
4. `npm config get omit` —— 看 npm 自己有没有默认 `optional`

## 防御性建议

- 装 claude-code 用 `npm install -g @anthropic-ai/claude-code --include=optional` 显式带上
- 或者用 Anthropic 官方 install script（curl-based），它会自己处理 platform binary
- 别相信 stub 的报错路径，第一时间 `type` 一下被劫持的 shell 命令
