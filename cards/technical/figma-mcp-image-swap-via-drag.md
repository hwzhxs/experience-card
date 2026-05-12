---
title: Figma MCP 图片替换用 user drag，不要程序上传
category: technical
created: 2026-04-21
tags: figma, mcp, mockup, automation
---

## 原则

用 Figma MCP (`use_figma`) 自动化替换 frame 里的图片时，**不要尝试用 `figma.createImage(bytes)` 程序上传**（字节数组太大 MCP code 参数 50K 限制传不过去，外部 URL 上传被 sandbox 挡）。

**更好的做法**：让用户把 PNG 文件**直接拖进 Figma canvas**，然后脚本去找孤儿图片并映射到目标位置。

## 完整工作流

1. **用户导出 4 张图** → 拖进 Figma canvas 任意位置
2. **图片落在画布上** → 作为 orphan rectangles，带 image fills
3. **脚本扫描 Page** 找所有 `name` 符合 `shopping 1` / `parcel 1` 等模式的 rectangles
4. **对每个 orphan**：
   - 读 `orphan.fills`（image fill 带 hash）
   - 找到对应 clone frame 里的 `IMG_xxx` rectangle
   - `target.fills = orphan.fills`（fill 转移）
   - `target.resize(w, h)` + `target.x=0, y=0` 填满
   - 隐藏 `IMG_xxx2`（第二层原图）
   - `orphan.remove()` 清理

## 何时用

- 需要批量替换 Figma frame 里的图片内容
- MCP 工具链（没有直接的 Figma 写 API）
- 图片来自用户本地（不在公网 URL）

## 对比

| 方案 | 可行性 | 复杂度 |
|---|---|---|
| `figma.createImage(bytes)` + base64 inline | ❌ 50K code 限制 | 极高 |
| 上传到公网 URL + `createImageAsync` | ❌ sandbox 挡 | 中 |
| Figma REST API + PAT | ✓ 但要 token | 中 |
| **用户拖 + MCP 映射** | ✅ | **低** |

源头：Edge Mobile Nudge 专题 4 scenario mock 替换 (2026-04-21)
