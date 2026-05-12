---
title: Audit Before Deploy
category: methodology
source: retro 2026-04-11
status: promoted
---

建完系统后先做全面审计（脚本健壮性、数据流闭环、MCP 可达性），再部署上线。

2026-04-11 建完 Self-Evolution System（8 个新脚本 + MCP 改动 + 7 个 launchd 任务），做了三路并行审计，发现 4 个 P0 + 8 个 P1 bug：
- `set -e` + Claude CLI 返回非零 = 脚本静默死亡（全部 8 个脚本）
- `--max-tokens 16000` 不是合法 CLI flag（evolution-weekly 永远跑不起来）
- `memory_vectors WHERE memory_id` 列名错误（孤儿 embedding 永远不清理）
- RSS 内容内嵌 prompt = shell 注入风险

如果跳过审计直接部署，至少 2 个核心脚本会永远失败且无日志。

**原则**: 系统越复杂，审计越必要。不是"写完就完"，是"写完 → 审计 → 修复 → 再部署"。
