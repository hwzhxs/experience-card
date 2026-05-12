---
title: idempotent-automation-completion-markers
category: methodology
source: auto-mined 2026-04-20
status: draft
confidence: 0.85
---

Scheduled automation scripts must be idempotent: check for a completion marker (unique string in output file, DB flag, or sentinel comment) before running and skip if already done. Also implement self-heal — on failure, retry with context rather than silently dropping. Three-layer defense: (1) verify prerequisites exist, (2) detect prior completion marker, (3) pass context to downstream consumers. Evidence: daily-digest 幂等检测 '## 🤖 AI 分类摘要' marker pattern + self-heal mechanism in data-extract, both independently converged on same pattern across two separate sessions.
