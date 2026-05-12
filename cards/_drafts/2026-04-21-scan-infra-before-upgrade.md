---
title: Before upgrading or writing a new adapter, scan what's already running
category: methodology
source: retro 2026-04-21
status: draft
---

When you hit a version-compatibility wall (tool X dropped API Y, your backend only has API Z), resist the impulse to downgrade X or build a translator proxy yourself. **First inventory what's already running locally** — containers, launchd jobs, background services. On 2026-04-21 I was about to downgrade Codex CLI or write a chat↔responses translator proxy when the user asked "docker 里的 llm 看看能支持吗". One `docker ps` revealed a LiteLLM container already proxying GitHub Copilot and natively exposing `/v1/responses` — the exact thing we needed. The fix was 3 lines in `~/.codex/config.toml`. The general principle: version-compat problems usually already have a solution somewhere in your own machine's infrastructure (LiteLLM, nginx, existing proxies, language-server orchestrators, MCP wrappers). Spend 2 minutes on `docker ps` / `launchctl list` / `lsof -i -P | grep LISTEN` before spending 4 hours writing glue. Running infra > unwritten infra.
