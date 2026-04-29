---
title: 跨机用户名同步陷阱：plist 路径硬编码 vs wrapper $HOME
tags: macos, launchd, multi-machine, paths, plist
created: 2026-04-29
---

# 跨机用户名同步陷阱：plist 路径硬编码 vs wrapper $HOME

## 症状

第二台 Mac 用户名跟主机不同（主机 `/Users/xiaosong/`，副机 `/Users/xiaoszhang/`）。把 `~/Library/LaunchAgents/com.hive.team-kb-{push,pull}.plist` 整个 cp 过来后，`launchctl load` 不报错、`launchctl list` 显示 PID，但实际啥都不做：log 里写 `wrapper: /Users/xiaosong/Library/Hive/team-kb-push-wrapper.sh missing`。

## 根因

plist 里 4 处硬编码 `/Users/xiaosong/`：
- `ProgramArguments` 指向 wrapper 脚本绝对路径
- `WorkingDirectory`
- `StandardOut/ErrorPath`
- `EnvironmentVariables.HOME`

launchd 不展开 `$HOME` 或 `~`，plist 是 plain XML，必须给绝对路径。

`~/Library/Hive/team-kb` symlink 同样写死了 `/Users/xiaosong/SongClub/personal/projects/team-kb`，副机上是死链。

## 修复

```bash
sed -i '' 's|/Users/xiaosong/|/Users/xiaoszhang/|g' \
  ~/Library/LaunchAgents/com.hive.team-kb-{push,pull}.plist
# HOME env var 没尾随斜杠，单独再来一次
sed -i '' 's|<string>/Users/xiaosong</string>|<string>/Users/xiaoszhang</string>|g' \
  ~/Library/LaunchAgents/com.hive.team-kb-{push,pull}.plist
ln -sfn ~/SongClub/personal/projects/team-kb ~/Library/Hive/team-kb
launchctl unload ...; launchctl load ...
```

## 设计教训

**plist 必须硬编码用户路径 → wrapper 脚本里用 `$HOME`** 才能让 plist 跨机器幂等：

- ❌ plist 直接 `ProgramArguments = ["/bin/zsh", "/Users/xiaosong/.../auto-push.sh"]`
- ✅ plist `ProgramArguments = ["/bin/zsh", "/Users/xiaosong/Library/Hive/wrapper.sh"]`，wrapper 内部 `SCRIPT="$HOME/Library/Hive/team-kb/scripts/auto-push.sh"`

仍要修 plist 里的用户名一处，但 repo 里的 script 路径不会跨机失效。Hive 的设计就是这样（wrapper 注释明确写了原因）。

## 补充

跨机同步 plist 的 4 个常错点：
1. `Label` —— 一致就行
2. `Program*` —— 必须跨机绝对路径，**只能 sed**
3. `WorkingDirectory` —— 同上
4. `EnvironmentVariables.HOME` —— 同上，sed 时记得这个不带尾斜杠

或者考虑：plist 不进 git，部署时用 envsubst 之类生成。
