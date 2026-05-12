---
title: Dry-run real data before committing to a plan
category: methodology
source: retro 2026-04-21
status: draft
---

When a plan hinges on "how much data is there / what's the coverage", **run the actual scan before finalizing the plan** — not after. In the SongClub graph project, the original plan was "补 164 文件的 frontmatter"; a dry-run (5 commands: `find` + 3 `grep` + 1 `wc`) showed `[txt](path.md)` links already exist in the body (94 edges across 21 files), which collapsed "补 frontmatter" from mandatory to optional. The plan pivoted from "Route A frontmatter backfill" to "Route B wiki-link extract + D embed + F entity" — **zero manual backfill**, 5-8 hours saved. The insight isn't about graph-building; it's that empirical measurement of the current state routinely invalidates assumptions the plan was built on. Before committing to any approach that involves "we'll need to do X for all files", run the 1-minute scan: do the files already have what you'd be adding?
