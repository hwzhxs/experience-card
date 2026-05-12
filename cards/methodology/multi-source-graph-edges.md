---
title: Knowledge graph edges — use 3+ sources, don't pick one
created: 2026-04-21
source: SongClub dashboard graph implementation
---

Building a personal knowledge graph, resist picking a single edge source. Each source alone is weak; 3 layered together give a usable graph with zero manual backfill.

**Three sources, each fixing the others' blind spots:**
- **B (hard link)** — scan `[text](path.md)` + `[[wiki]]` in the md body. Author-declared, 100% accurate, but only lives where the author already wrote links. In SongClub: 50 edges across 21 of 106 files — too sparse alone.
- **D (embedding cosine)** — fuzzy semantic neighbors. Catches hidden connections the author didn't link explicitly. But noisy; threshold matters. In SongClub: 95 edges at cos >= 0.72 (0.78 was too strict, gave only 26).
- **F (entity co-mention)** — extract canonical entity names (people/projects) from an existing store (Neo4j in our case), scan each file, files co-mentioning an entity get linked. Cheap, very high signal, works even when the author wrote no links. In SongClub: 487 edges (biggest single source).

**Why this beats single-source approaches (like Hive's frontmatter-only):**
- Zero backfill cost — don't need to add `related:` fields to 163 existing files
- Resilient — if Neo4j is down, F drops out but B+D still produce a graph
- Edges are typed and visually distinguishable (solid/dashed/dotted), so user sees *why* two nodes connect

**When NOT to do this:** if you have strong curation discipline and every file gets frontmatter anyway, the Hive approach is simpler. This pattern is for orgs/personal systems where back-filling metadata across N files is unrealistic.
