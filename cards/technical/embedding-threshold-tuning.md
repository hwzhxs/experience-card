---
title: Embedding similarity threshold — start at 0.72, not 0.78
created: 2026-04-21
source: SongClub graph-build.js tuning
---

For Azure OpenAI `text-embedding-3-small` (1536-dim) over personal knowledge notes (project docs, people profiles, experience cards), cosine similarity threshold around **0.72** is the right starting point — not the 0.78+ you'd see in RAG tutorials.

**Numbers from SongClub's 106-node graph:**
- `cos >= 0.78` → only **26 edges**, ~42 orphans, embedding layer barely helps
- `cos >= 0.72` → **95 edges**, 36 orphans, embedding contributes meaningfully alongside link+entity edges
- Didn't need to go below 0.72; below that you'd start getting "two files happen to both be markdown" noise

**Why the public tutorials use higher thresholds:**
RAG retrieval tops a small K (usually 3-5) from a **narrow scoped query** — a specific question. High threshold filters precision. Knowledge graph edges are different: you're comparing **whole documents** to find neighbors, and personal notes have more stylistic overlap than a query↔passage match. Same library, different math.

**Practical rule:** always expose threshold as a parameter, run `--dry-run` twice (strict + loose), pick the middle. Don't trust defaults from tutorials that assumed a different retrieval shape.
