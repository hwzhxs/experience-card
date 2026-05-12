---
title: Zoned File Governance
category: concepts
created: 2026-04-14
source: SongClub LLM Wiki 混合架构设计
confidence: 0.9
---

给 LLM 编辑权限时，文件按段落分区：不可变区域（基本信息、性格、原话、时间线）LLM 不碰，可演化区域（状态、当前关注、下一步、关联）LLM 可读写更新。用 `<!-- editable: start/end -->` 标记边界，`<!-- updated: DATE -->` 留审计注释。
