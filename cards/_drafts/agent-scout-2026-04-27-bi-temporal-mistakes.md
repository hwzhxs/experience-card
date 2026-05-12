# Bi-temporal Mistake Tracking
> Source: Agent Architecture Scout — 2026-04-27

## Principle
A debugging note is only useful while the code state it describes still holds. Tag each "lesson learned" entry with the temporal scope it applies to (date verified, commit SHA of relevant file, or external API version). Periodically re-check; if the referenced surface has changed beyond recognition, mark the entry stale instead of letting it silently mislead.

## Context
Applies to accumulated debugging knowledge bases — `COOKBOOK.md`, experience cards in `technical/`, "踩坑集" sections. Without a validity dimension, agents keep injecting fixes for problems that no longer exist (warning about an API quirk that was patched, or a workaround for a flag that was removed). Worse, the agent might "fix" working code by re-applying the obsolete workaround.

## Evidence
- **engramX** ([github.com/NickCirv/engram](https://github.com/NickCirv/engram)) implements **bi-temporal memory**: past failure nodes carry temporal awareness, and "refactored-away mistakes stop firing." Mistakes are re-checked against current LSP diagnostics; if the referenced symbol/file is gone, the warning is suppressed.
- Same idea shows up in code-search literature as "validity intervals" on indexed facts.

## Application to SongClub
- `COOKBOOK.md` entries should carry a `last_verified: YYYY-MM-DD` line (and optionally `applies_to: <file or external API>`). Add to template at top of file.
- Weekly job (could piggyback on `memory-consolidator.js` Job 4 freshness check or `kb-lint.mjs`): for each entry, check if the referenced file/API still exists / responds the same way; if not → flag in `cache/cookbook-stale.md`.
- For external-API entries (Graph API, Superset, Figma): annotate with API version captured at time of writing; re-test on cadence.
- Doesn't require deleting entries — just demoting them visually so an agent reading the file sees "⚠️ unverified since 2025-09" before adopting the workaround.
