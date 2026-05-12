# Active Memory Revision
> Source: Agent Architecture Scout — 2026-04-20

## Principle
Memory stores shouldn't just append + prune. A periodic background job should score memories, pick low-confidence / contradictory / stale entries, and have an LLM **rewrite** them using neighboring context. Memory improves over time instead of just growing.

## Context
Applies to any long-running agent with a persistent memory store. The alternative — append-only + eventual prune — loses fidelity as the same fact gets restated in inconsistent ways across months.

## Evidence
- salishforge/memforge — https://github.com/salishforge/memforge — 10-phase sleep cycle with explicit **Revision** phase where an LLM rewrites flagged memories using entity/neighbor context, and **Reflection** extracting condition→action rules. Quality signals (revision stability, retrieval correlation, contradiction rate) are self-measured.

## Application to SongClub
Add Job 6 to `scripts/memory-consolidator.js`: pick N lowest-confidence or least-accessed entries weekly, pass each with its top-k semantic neighbors to Claude with a "merge, rewrite, or forget" prompt. Route results to `personal/cache/revision-review.md` for human review before committing. Requires a `confidence` column on `memories` (seed from access frequency).
