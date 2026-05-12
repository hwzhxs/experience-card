---
title: Zoned Governance for LLM-Editable Files
category: methodology
created: 2026-04-13
source: LLM Wiki 混合架构设计 session
confidence: 0.9
---

When giving LLM edit access to knowledge files, don't make the whole file editable. Analyze each section by two criteria: (1) does it go stale? (2) can LLM update it without introducing noise? Mark boundaries with HTML comments (`<!-- editable: start/end -->`). Immutable sections preserve human-curated quality; evolvable sections stay fresh automatically.
