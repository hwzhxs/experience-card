---
title: LLM Wiki Pattern
category: concepts
created: 2026-04-14
source: Karpathy LLM Wiki (gist) + 铁锤人微信文章
confidence: 0.9
---

LLM 主动维护结构化知识网络：每次新信息进来，读已有页面 → 判断该改哪里 → 重写/补充/加交叉链接。核心区别于 RAG（每次从零检索拼凑）和 append-only（只往末尾追加）。SongClub 采用混合方案：editable 段落允许 LLM 更新，不可变段落保持审计轨迹。
