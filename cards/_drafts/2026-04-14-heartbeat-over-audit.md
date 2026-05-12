---
title: Heartbeat Over Audit
category: methodology
source: retro 2026-04-14
status: draft
---

监控 dashboard 不能只显示 audit log（有事才记），必须同时显示心跳信号（poll/轮询记录）。否则空闲期看起来像系统挂了。KoBee dashboard 最初只读 audit JSONL，一小时没消息就像死了一样；改为同时 tail kobee.log 后才能看到每次 poll 的 "no new messages"，确认系统活着。Liveness ≠ Activity。
