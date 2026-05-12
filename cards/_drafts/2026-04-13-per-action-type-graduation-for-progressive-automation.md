---
title: Per-action-type graduation for progressive automation
category: design-craft
source: auto-mined 2026-04-13
status: draft
confidence: 0.78
---

Progressive automation should graduate at the action-type level, not the feature version level. Instead of 'v1 = suggest all, v2 = automate all,' each action type earns automation rights independently based on its own intent accuracy threshold. This reframes an automation feature from 'two-phase rollout' into 'a continuously learning action permission system.' Actions that hit the accuracy threshold get automated; those still learning remain in suggestion mode. Graduation criteria must be tied to measurable accuracy, not product milestone.
