---
title: Infrastructure Value = Data × Usage Frequency
category: methodology
created: 2026-04-13
source: Neo4j Graph 集成 session
confidence: 0.85
---

Building infrastructure (database, graph, index) without integrating it into daily workflows creates dead weight. The value equation is Data × Usage Frequency — a well-seeded Neo4j with 199 entities but zero daily queries has near-zero value. Always wire new infrastructure into at least one automated consumer before considering it "done".
