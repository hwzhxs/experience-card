---
title: Audit own system before comparing to external
category: methodology
source: retro 2026-04-21
status: draft
---

When comparing our system to an external one (e.g., Hive-agent vs SongClub), **audit our own current state FIRST, then read the external codebase** — not the other way around. In the 2026-04-21 Hive comparison, I wrote a comparison report three times: each version got corrected because I claimed SongClub "didn't have" features that it actually had (dashboard, experience cards, Skills ecosystem, retro flow, taste library). The pattern: I had a stereotyped mental model of SongClub ("personal agent with memory+M365"), read Hive fresh, and kept listing Hive features as "unique" when they existed on both sides. Each round of "we already have this" correction cost time and credibility. Fix: before any external comparison, spend 10 minutes running `ls`/`grep` on our own relevant directories, listing actual features, THEN compare. Treat the current codebase as truth, not memory. Especially true when the user has been iterating on the system for months — you're comparing snapshots from different time points in your head.
