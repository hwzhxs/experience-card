# Novelty Gate on Memory Writes
> Source: Agent Architecture Scout — 2026-04-27

## Principle
Don't store every observation — gate writes by *prediction error*. Before inserting into a long-term memory store, check semantic similarity against existing memories; skip if too redundant. The interesting signal is what's *surprising*, not what's repeated.

## Context
Applies to any agent system where `remember()` / `add_memory()` is called liberally. Without a write-side filter, the store accumulates near-duplicates that cost embedding compute, bloat retrieval candidates, and force expensive consolidation/dedup jobs downstream. A cheap cosine-distance check at write time prevents the rot.

## Evidence
- **vestige** ([github.com/samvallad33/vestige](https://github.com/samvallad33/vestige)) implements **prediction error gating** as one of 30 cognitive modules — "selectively stores only surprising or novel information."
- Contrast: most agent memory layers (including SongClub's `local-memory` MCP today) accept any `remember()` unconditionally and rely on consolidator passes to dedup.

## Application to SongClub
- Add a novelty check to `remember()` in `mcp/local-memory/`: before insert, embed the new content, query top-3 nearest in `memory_vectors`. If max cosine ≥ 0.92, skip the insert and log a "duplicate skipped" event (or merge tags/metadata into the existing row).
- Reduces work for `memory-consolidator.js` Job 2 (dedup report) — most duplicates never enter the store.
- Threshold tunable; start conservative (0.95) and lower as we calibrate against the existing dedup report.
- Doesn't replace consolidator — periodic dedup still catches drift across phrasings — but shifts the curve.
