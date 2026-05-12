---
title: Playwright DOM 抓取要覆盖多种数据容器
created: 2026-04-15
trigger: Copilot Scorecard User Persona 页面返回 0 表格，数据实际在 .summary-card 里
category: technical
---

## 洞察

Web dashboard 的数据不一定都在 `<table>` 里。同一个站点的不同页面可能用完全不同的 DOM 结构承载数据：

- **表格页面** → `<table>` + `<thead>` / `<tbody>`
- **KPI 卡片页面** → `.summary-card` / `.stat-card` / `.metric-card`
- **图表页面** → Canvas（无法 DOM 抓取，需要拦截 API 或读图例）

## 教训

写 Playwright 抓取脚本时，不能假设"所有数据都在表格里"。应该：

1. 先用 `--visible` 模式或 DOM dump 调试实际结构
2. 对每种数据容器写独立的提取逻辑
3. 在输出 JSON 里用不同字段区分（如 `tables` vs `cards`）

## 具体案例

Copilot Scorecard（`scripts/copilot-scorecard.js`）：
- 大部分页面（Scorecard/Feedback/Feature Intents 等）→ `<table>` → `data[slug].tables`
- User Persona / User Segments → `.summary-card`（children: label + value + trend）→ `data[slug].cards`
- 修复方法：在表格提取后加一段 `.summary-card` 提取逻辑

## 调试技巧

快速 dump 目标数据的 DOM 位置：
```js
// 找到包含数值的元素，反查其 parent 的 class
const candidates = document.querySelectorAll('div, span');
for (const el of candidates) {
  const t = el.textContent?.trim();
  if (/^\d[\d,.]*[MK]$/.test(t)) {
    console.log(t, el.className, el.parentElement?.className);
  }
}
```
