---
title: launchd-consolidate-by-time-window
category: methodology
source: auto-mined 2026-04-20
status: draft
confidence: 0.73
---

Group launchd jobs by time window (e.g., one morning-run.plist for all 11:00 tasks) rather than one plist per script. Archive old plists to _archive/ instead of deleting — preserves rollback and audit trail. Fewer plists = fewer TCC permission entries, easier log tracing. Pattern emerged from 12→4 plist consolidation + TCC exit 78 debugging across two sessions.
