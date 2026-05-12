---
title: Direct Execution Over Nested CLI
created: 2026-04-11
trigger: script shells out to Claude CLI for batch data operations
---

# Direct Execution Over Nested CLI

## Principle
When a shell script calls Claude CLI (`claude -p "..."`) to do batch operations (like graph seeding, bulk API calls), it's slower and more fragile than doing the same work directly in the current session.

## Why
- Nested CLI has no access to current session's MCP tools — it needs its own setup
- `timeout` and other system commands may not exist on all platforms (macOS vs Linux)
- Error handling is opaque — failures get swallowed into log files
- The outer session already has all the context and tools loaded

## Rule
If you're already in a Claude Code session with the right MCP tools:
1. **Read the data sources yourself** (the files the script would read)
2. **Call the tools directly** (graph_add, remember, etc.)
3. **Keep the script for unattended/scheduled runs** — fix platform issues (like `timeout`) but don't rely on it during interactive sessions

## Exception
Scheduled/cron jobs that run without a human session — those need the CLI script. Fix it to be cross-platform.
