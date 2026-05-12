# Progressive-Disclosure Retrieval
> Source: Agent Architecture Scout — 2026-04-20

## Principle
Layer memory retrieval into cheap-index → context → detail stages. Return compact IDs + hooks first, let the caller pick, then fetch full content only for chosen IDs. Saves ~10× tokens vs returning full-text top-N.

## Context
Applies to any agent recall / RAG tool where the caller is an LLM. Especially valuable when the memory corpus grows past a few hundred entries and each entry can be multi-hundred tokens.

## Evidence
- thedotmack/claude-mem (63k⭐) — https://github.com/thedotmack/claude-mem — explicit `search` → `timeline` → `get_observations` pipeline, claims ~10× token savings.

## Application to SongClub
Extend `local-memory` MCP: add `recall_index(query, limit)` returning `[{id, 1-line hook, tags}]` only, pair with `recall_detail(ids=[...])`. Keep existing `smart_recall` as convenience wrapper that does both steps for small N. Also maps to Daily Digest: instead of reading every people/project file, read an index first and only fetch files the router decides matter.
