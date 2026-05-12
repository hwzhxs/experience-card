---
title: 抄上游项目前先对照自己已有实现再定 port 范围
created: 2026-04-24
source: SongClub porting hive-agent v2.6 UNIFIED persistence rule
---

看到上游有好功能，自然倾向 wholesale port。但自己项目可能**已经有等价结构**，只是名字不同。这时整套 port 是 churn，不是收益。

典型触发场景：上游文档看起来设计得很漂亮（hive 的 UNIFIED 三写入源 + per-person 文件夹 + `## Interaction:` 格式），对比之下自己 repo 显得"缺"。先停一下，recon 自己当前实现：SongClub 已有 `roster/people/{slug}.md` + `## 动态时间线` section + 3-zone governance + daily-digest 深度写 + `- YYYY-MM-DD:` 格式 dedup —— 结构上**接近等价**，只是层级展开方式不同。真正缺的只有 3 件：(1) quarterly archive (2) session retro 写入收敛规则 (3) KoBee inline-write 入口。

工作量从"wholesale 迁移到 per-person 文件夹 + 全流程改写"缩到"3 个小增量 + 不动主文件结构"，省数小时 churn。

**方法**：port 之前花 15 分钟 `grep`/`find`/读一个真实样本，把上游的"feature list"和自己的"当前状态"画个 2 列表。**只 port 真缺的那几列**。上游的"命名/结构"如果不影响功能，保留本地的。
