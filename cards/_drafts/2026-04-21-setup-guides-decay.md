---
title: SETUP-GUIDEs decay fast — verify version assumptions before copying
category: methodology
source: retro 2026-04-21
status: draft
---

When following someone else's SETUP-GUIDE (Hive's, a team wiki, a StackOverflow answer), check **when it was last updated** AND **which tool versions it assumes** before copying commands blindly. Tools that evolve fast (Codex CLI, MCP SDKs, AI provider APIs) break config schemas quietly — a guide written 4 weeks ago may point at deprecated endpoints. On 2026-04-21, Hive v2.3's SETUP-GUIDE.md:2145 said `wire_api = "chat"`; Codex 0.121 (our version) had removed that value 2.5 months earlier (hard removal 2026-02-01, per OpenAI Codex discussion #7782). Blindly copying wasted an hour before we realized the guide itself was stale. The fix: read the publish date in the guide, check the current version of the tool it configures, and **scan the tool's changelog** for "breaking" or "deprecation" entries between those two dates. If you find a protocol-level change, stop and design your own path. Sibling principle to "audit own system before comparing to external" — both stem from *don't trust your mental model of when things work, verify against live state*.
