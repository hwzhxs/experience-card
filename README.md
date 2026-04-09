# Experience Cards

Atomic work principles extracted from real experience. One card = one insight.

Used by all Claude Code agents via `~/.claude/CLAUDE.md` — every session loads the index, reads relevant cards on demand.

## Structure

```
cards/
├── design-craft/     Design principles (typography, color, layout)
├── review/           Design review methodology
├── presentation/     Writing and presenting to stakeholders
├── communication/    Team communication style
├── technical/        Technical principles (not bug fixes — those go in COOKBOOK)
└── methodology/      Work process and system design
```

## Card Format

```markdown
---
title: Short descriptive title
created: YYYY-MM-DD
source: conversation | retro | correction
---

The principle. One paragraph max.
```

File naming: english kebab-case, e.g. `typography-no-competition.md`.

## How Cards Grow

- Session retro: "Did I learn something reusable?" → write card
- Correction: user corrects a recurring mistake → extract principle
- Never forced — 0 new cards per session is normal

## What Does NOT Belong Here

- Bug fixes with code → COOKBOOK.md
- Specific config/IDs → memory.db
- Transient project state → personal/
- Visual design taste → ~/taste/
