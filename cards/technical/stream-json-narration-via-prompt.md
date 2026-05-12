---
title: Claude Code stream-json 进度播报靠 prompt 让 LLM 说人话，不 post 机械 tool events
created: 2026-04-24
source: SongClub KoBee stream-json narration (from hive v2.6 self-chat-monitor)
---

用 `claude -p --output-format stream-json --verbose` 做 real-time progress 时，**不要** post 机械的 `tool_use` / `tool_result` 事件到用户界面（Teams / Slack 等）。那种 "🔍 mcp__foo__get_bar input={...}" 是噪音 + 易触发 HTML 转义坑 + 对用户意义低。

正确做法：
1. **Prompt 里明确要求** LLM 在每次 tool call 前后用 **一句自然语言**说明当下在做什么。例如："BEFORE each tool call, write ONE short sentence in casual Chinese-English mix saying what you're about to look up."
2. **onEvent 只 surface `assistant` event 的 `text` block** —— 把每段当独立消息 post。`tool_use` / `tool_result` 忽略。
3. **序列化 post** 通过 `postChain = postChain.then(async () => ...)` 保证到达顺序（异步 chat API 可能乱序）。
4. **fallback**：streaming 发了 0 条 → 用 `result` event 的 final text 发一条兜底。
5. 最终 reply 格式要求**独立成立**（即使用户没看到中间 narration 也能理解）。

好处：用户看到的是"我去查下邮件 → 找到 5 封 → 正式回答"这种自然流，不是 JSON 倾泻。Prompt 一两行就能 steer LLM，比写 post-processor 简单。

参考实现：hive-agent `scripts/self-chat-monitor.js:1130-1142`，SongClub `scripts/teams-auto-monitor.js` `llmWithTools` + `spawnClaudeStream`。
