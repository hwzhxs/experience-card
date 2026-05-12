---
title: Figma 自动化用 REST API + PAT，不用 MCP OAuth
created: 2026-04-14
trigger: Figma 自动化 / 定时任务读 Figma / claude -p 调 Figma
---

# Figma 自动化用 REST API + PAT，不用 MCP OAuth

Figma MCP（`figma-remote-mcp`）的 OAuth 认证只在交互式 Claude Code session 中有效。`claude -p` 新进程无法继承 OAuth token，会弹认证 URL 然后卡住。

**解决方案**：用 Figma REST API + Personal Access Token (PAT) 直接 curl，绕过 MCP。

```bash
curl -s -H "X-Figma-Token: $FIGMA_TOKEN" \
  "https://api.figma.com/v1/files/{fileKey}"
```

- PAT 存在 `.env` 的 `FIGMA_TOKEN` 字段
- REST API `GET /v1/files/{fileKey}` 支持 FigJam board，返回完整节点树含 sticky notes
- **限制**：REST API 不返回 sticky note 的 `authorName`（MCP 的 `get_figjam` 会返回）
- 适用于所有需要自动化（launchd / cron）读 Figma 的场景
- 交互式 session 内仍优先用 MCP 工具（有 author 信息更完整）
