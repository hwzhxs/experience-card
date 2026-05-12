---
title: macOS TCC 会锁住 Desktop/Documents 里的文件
tags: macos, tcc, permissions, provenance, debugging
created: 2026-04-23
---

# macOS TCC 会锁住 Desktop/Documents 里的文件

## 症状

长 session 中，Claude Code 突然对 `~/Desktop/...` 或 `~/Documents/...` 里的某个文件报：

```
EPERM: operation not permitted, open '...'
```

甚至 `cat` / `rm` / `mv` 都失败。常常**一个文件锁了，同目录下的其他文件也跟着锁**。重启 Claude Code 就恢复。

## 原因

macOS Sonoma+ 给受保护位置（Desktop / Documents / Downloads / iCloud Drive / 可移动卷）里的文件自动打 **`com.apple.provenance` 扩展属性**，记录创建者身份。TCC 基于这个做 per-file 授权。

Session 中以下任一情况会让 token 失效或 provenance 不匹配：
- 文件被其他进程触碰（iCloud 同步 / Finder QuickLook / Spotlight / 编辑器保存）→ 重新打 provenance 标
- Claude Code 派生子进程（bash / python）→ 子进程 TCC 上下文和父进程不完全一致
- TCC token 过期（session-scoped 缓存）
- 某个文件触发 Gatekeeper，锁定后连带锁同目录

重启 Claude Code 拿新 token，校验通过，恢复。

## 诊断

```bash
ls -la@ <file>              # @ 表示有扩展属性
xattr <file>                # 查属性列表（可能自己也被拒）
head -c 1 <file>            # 最直接的访问测试
```

## 根治：搬到非 TCC 保护位置

macOS 默认保护的系统预定义目录：`Desktop / Documents / Downloads / Library / Movies / Music / Pictures / Public / Applications / Sites`。

搬到 `~/` 根下任何**非预定义**名字的目录即可：

```bash
mv ~/Desktop/MyProject ~/MyProject       # 扁平
# 或
mkdir -p ~/code && mv ~/Desktop/MyProject ~/code/   # 套一层
xattr -rc ~/MyProject                    # 清掉历史污染
```

## 应急：不搬家的临时解法

```bash
xattr -rc <project-dir>      # 递归清扩展属性（需要重跑 Claude Code）
```

每次卡住跑一次，等于手动模拟重启 token。

## 检查清单（搬家后）

搬完目录必须 grep 所有**外部**配置的绝对路径，否则会出现「目录在，但 Claude Code 看不见」的幽灵 bug：

**最先查 —— Claude Code / MCP 运行时配置：**
- `~/.claude.json` 的 `mcpServers` / `projects.*.mcpServers` —— MCP server command args 里是绝对路径，不改完这里，每次 startup 都会 silently fail 连接。而且 startup 横幅 `"N mcp server failed"` 的计数**不准**（漏报），要用 `claude mcp list` 确认真实状态
- `~/Library/LaunchAgents/com.*.plist` —— ProgramArguments / WorkingDirectory / EnvironmentVariables 常硬编码绝对路径
- Claude Code 的 `memory.db` 里 `repo-insight` metadata 的 `repo` 字段

**项目自带可能残留：**
- `package-lock.json` / `node_modules/.package-lock.json` — npm 偶尔内嵌绝对路径（`npm install` 刷新）
- `.vscode/workspace.json` / `.idea/` — IDE workspace 有时保存绝对路径
- Playwright / Jest 配置
- Shell alias / bookmark

一次性扫描命令：
```bash
grep -rn "Desktop/<OldName>" ~/.claude.json ~/Library/LaunchAgents/ ~/.config 2>/dev/null
```

历史日志（`history.jsonl`、`plans/*.md`、`*.bak`、`skills-disabled/`）里的命中不用改，不影响 runtime。

## 为啥 `/tmp` 不受影响

`/tmp`, `/usr/local/...`, `~/` 根下自建目录都不是 TCC scope，访问 provenance 不校验。适合做临时工作目录。
