---
title: LLM-editable files need three zones, not two — Immutable / Append-only-prepend / Editable rewrite
category: methodology
source: retro 2026-04-22
status: draft
---

The generic advice "mark editable regions explicitly" (see `zoned-governance`) is not enough when a downstream parser depends on format inside the non-editable region. Today's Hive upgrade forced the refinement: `personal/roster/people/*.md` has three zones, not two. **Zone 1 Immutable** (basic info, personality, historical timeline) — LLM must not touch. **Zone 2 Append-only prepend** (`## 动态时间线` / `## 进展记录`) — LLM may only prepend one new `- YYYY-MM-DD: {...}` bullet at the top, must preserve exact regex format because `personal/dashboard/app.js:1147` depends on it. **Zone 3 Editable rewrite** (inside `<!-- editable: start/end -->`) — LLM may rewrite the whole segment and add an `<!-- updated: DATE -->` marker. The mental model that makes this easy to miss: "immutable means I can't touch anything except editable". The right model: "there's a third category where I can write but only in exactly one shape". Apply anywhere an LLM writes to a file that a deterministic downstream parser also reads. If you collapse to 2 zones, either the append path violates "byte-for-byte" or the parser breaks on drift.
