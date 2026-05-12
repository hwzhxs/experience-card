---
title: Separate File Writing from Database Writing
category: methodology
created: 2026-04-14
source: Graph 双写问题 — daily-digest Step 8.3 删除
confidence: 0.85
---

Don't let one process both write files AND write databases with the same data. daily-digest was writing relationship info to both `## 关联` paragraphs and Neo4j graph_add — two sources of truth, sync risk. Fix: digest writes files only, graph-seed reads files and writes Graph. Single responsibility per process, files as the source of truth.
