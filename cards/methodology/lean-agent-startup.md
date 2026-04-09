---
title: Agent startup files should be lean pointers
created: 2026-04-09
source: retro from memory.db + Hive experience
---

Keep agent startup files (CLAUDE.md, MEMORY.md, index files) lean — pointers only, never full content. Load details on demand when the topic comes up. This saves context window tokens and keeps every session fast to start. Indexes should stay under ~50 lines.
