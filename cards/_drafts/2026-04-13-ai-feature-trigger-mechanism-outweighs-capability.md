---
title: AI feature trigger mechanism outweighs capability
category: design-craft
source: auto-mined 2026-04-13
status: draft
confidence: 0.83
---

When designing context-aware AI features, the trigger mechanism (when and where to surface the capability) matters more than the capability itself. Poor trigger timing makes even great capabilities harmful — you cannot A/B test interruption timing because being interrupted changes user behavior. Prefer an invitation model over an interruption model: surface features at natural pause points (scroll stop, tab return) rather than proactively interrupting. The surface should follow the session context, not force users back to a specific app.
