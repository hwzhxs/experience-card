---
title: Experience Index
created: 2026-04-09
---

## Concepts
- [[cards/concepts/llm-wiki-pattern]] — LLM 主动维护知识网络：读已有内容 → 判断更新 → 重写/链接，而不是只追加
- [[cards/concepts/zoned-file-governance]] — 文件分不可变区域和可演化区域，用 editable 标记边界

## Decisions
- [[cards/decisions/no-knowledge-layer]] — 不新建 knowledge/ 目录，概念知识扩展到 experience-card 的 concepts category，避免增加系统复杂度

## Design Craft
(Cards will grow here from design work sessions)

## Review
(Cards will grow here from design review sessions)

## Presentation
- [[cards/presentation/data-driven-closing]] — Closing 要用数据对比说话，不要用抽象观点总结
- [[cards/presentation/three-act-structure]] — 分析报告用三幕结构：先全景再聚焦（宿主产品 → 嵌入功能 → 两者结合）
- [[cards/presentation/behavior-before-difference]] — Data reports: show user behavior first (who/what/satisfaction), then differences (platform/embed/compete)
- [[cards/presentation/narration-iterate-table-in-chat]] — 语音脚本一页一页改：聊天里 table 迭代 → 定稿后一次写入 Python
- [[cards/presentation/chinese-colloquial-narration-style]] — 中文 TTS 脚本重口语化（咱们/啊/呢/吧/说白了/你看）+ ElevenLabs Jessica 配置

## Communication
- [[cards/communication/english-names-only]] — All output must use English names, never Chinese names

## Technical
- [[cards/technical/spa-refresh-crossorigin]] — SPA refresh tokens require browser CORS origin to redeem
- [[cards/technical/spa-refresh-24h-family-preempt]] — SPA refresh token 有 24h family cliff（rotation 不重置），preempt at 23h 而不是等 24h 挂；`l3_captured_at` 要跨 rotation 不变
- [[cards/technical/stream-json-narration-via-prompt]] — Claude Code `--output-format stream-json` 做进度播报：不 post 机械 tool_use 事件，靠 prompt 让 LLM 自己说人话；onEvent 只 surface assistant text block
- [[cards/technical/teams-html-not-markdown]] — Teams renders HTML only, Markdown shows as raw text
- [[cards/technical/vtt-not-docx]] — Download meeting transcripts as .vtt, never .docx (IRM encrypted)
- [[cards/technical/macos-no-timeout]] — macOS lacks `timeout` command, use alternatives or remove
- [[cards/technical/figma-pat-over-mcp-oauth]] — Figma automation: use REST API + PAT, not MCP OAuth (CLI can't inherit OAuth)
- [[cards/technical/playwright-browser-sync]] — Playwright upgrade requires `npx playwright install`; old browser silently fails with empty data
- [[cards/technical/playwright-dom-diversity]] — Playwright DOM scraping must cover multiple data containers (table, summary-card, etc.), not just `<table>`
- [[cards/technical/figma-slides-clipboard]] — Figma Slides text via Playwright clipboard (Cmd+A→Cmd+C→readText); DOM/API/images all dead ends
- [[cards/technical/figma-mcp-image-swap-via-drag]] — Figma MCP 替换 frame 图片：让用户拖进 canvas，脚本找 orphan → 映射 fill，不要程序上传
- [[cards/technical/embedding-threshold-tuning]] — text-embedding-3-small 文档相似度从 0.72 起，不是 tutorial 里的 0.78+
- [[cards/technical/js-template-literal-nested-backticks]] — JS 模板字符串里不能嵌套反引号；LLM system prompt 里写示例特别容易踩，整个模块加载失败 → 所有 click handler 装不上
- [[cards/technical/macos-tcc-desktop-provenance]] — macOS TCC + com.apple.provenance 会在 long session 锁住 Desktop/Documents 文件（EPERM）；根治搬到 `~/` 非预定义目录，应急 `xattr -rc`
- [[cards/technical/esp32-arduino-core-3x-ledc-tone]] — ESP32 Arduino core 3.x 下 `ledcWriteTone` 不发声，必须追加 `ledcWrite(pin, 128)` 设非 0 duty；M5.Beep 0.1.1 踩此坑
- [[cards/technical/cross-username-launchd-paths]] — 跨机同步 plist 用户名硬编码会跨机挂；wrapper 用 `$HOME` 才幂等，plist 4 处需 sed（HOME env var 不带尾斜杠是常错点）
- [[cards/technical/claude-code-native-binary-install]] — `claude` 跑不起来三层叠：`.exe` stub 缺 +x → platform native package 是 optionalDependency 被 npm 跳过 → Claude Code 把 grep 劫持成调 claude 的 shell function，错误传染让诊断走死胡同。`type grep` 第一时间识别劫持

## Methodology
- [[cards/methodology/lean-agent-startup]] — Startup files should be lean pointers, load details on demand
- [[cards/methodology/memory-index-pointer]] — memory.db entries < 300 chars, details go to files with pointer
- [[cards/methodology/two-layer-persistence]] — Real-time save during chat + exit retro before session ends
- [[cards/methodology/direct-over-nested-cli]] — Do batch operations directly in session, not via nested Claude CLI
- [[cards/methodology/audit-before-deploy]] — Audit scripts/systems before deployment; complexity hides silent failures
- [[cards/methodology/audit-before-adding-layers]] — Audit existing layers before adding new ones; expand don't multiply
- [[cards/methodology/separate-file-from-db-writes]] — Don't let one process write both files and databases with same data
- [[cards/methodology/data-source-audit-before-use]] — Audit data source, scope, and platform before citing; mixing scopes kills credibility
- [[cards/methodology/weekly-report-principles]] — Weekly report: 2 Edge Mobile + 1 Agent, designer POV, no fabrication
- [[cards/methodology/zoned-governance]] — LLM-editable files need explicit boundaries; not all sections should be evolvable
- [[cards/methodology/infrastructure-times-usage]] — Infrastructure value = Data × Usage Frequency; unwired infra is dead weight
- [[cards/methodology/verify-server-cwd]] — 编辑前用 lsof 确认 server 工作目录与编辑目录一致，避免改错副本
- [[cards/methodology/stash-before-checkout]] — git checkout HEAD 会永久丢失未提交改动，恢复前必须 stash 或备份
- [[cards/methodology/multi-source-graph-edges]] — 个人知识图用 3 种边源（link + embedding + entity）叠加，别靠单一 frontmatter
- [[cards/methodology/audit-upstream-before-porting]] — 抄开源前先对照自己现有实现，画"feature vs 当前状态"2 列表；很多时候只需要增量 port 几项，不是 wholesale 迁移
