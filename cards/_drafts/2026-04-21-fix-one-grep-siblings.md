---
title: When fixing one injection point, grep the file for siblings
category: technical
source: retro 2026-04-21
status: draft
---

Security fixes come in families. When you fix a specific variable that flows into a dangerous construct (Cypher label, SQL string, shell arg, HTML sink), **grep the same file for the same pattern with other variable names** before calling the fix done. On 2026-04-21, I fixed `rel` interpolation into Cypher in three spots of `server.py` and declared it done. Codex's adversarial-review immediately caught that `graph_add()` also interpolated `etype` into a Cypher label **using the identical pattern**, one variable name away. Same file, same construct, different name — a 30-second `grep -n '`{' server.py` would have found it. The mental model that makes this easy to miss: "I'm fixing the `rel` bug" (singular). The right model: "I'm fixing Cypher label injection in this file" (plural). Apply everywhere any untrusted string meets a DSL construct — labels in Cypher, column names in SQL, attribute names in HTML, selectors in XPath. Fix one, scan all.
