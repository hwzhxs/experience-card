---
title: JS 模板字符串里不能嵌套反引号
tags: javascript, debugging, syntax
created: 2026-04-22
---

# JS 模板字符串里不能嵌套反引号

## 坑

用 JS 模板字符串（反引号）写多行 prompt / 文本时，**正文里别再出现反引号**。解析器不区分意图——看到 `` ` `` 就以为模板结束，把后面当 JS 代码解析。

```js
// ❌ 崩
const SYSTEM_PROMPT = `
Tabs are listed as `[id] (active?) Title`.
Call switch_tab...
`;
// SyntaxError: Unexpected token ')'
```

实际发生的：解析器把第一个 `` ` ` `` 当模板结束，然后 `[id] (active?)` 变成 JS 表达式，`(active?)` 语法错。

## 诊断症状

**主 module 整个不加载，下游所有 createXxxFeature 都不执行**，表现是：
- 页面渲染出来了（HTML 静态）
- 但**所有 click handler 失效**（attach 前就 throw 了）
- 看起来像 UI 冻结

排查：`node -e "import('./the-file.js').then(...).catch(e => console.error(e.message))"` 直接暴露语法错，比浏览器 DevTools Console 更干脆（浏览器的错提示位置有时被 sourcemap 搞模糊）。

## 修法

示例文本里改用双引号或反斜杠转义：

```js
const SYSTEM_PROMPT = `
Tabs are listed as "[id] (active?) Title".
`;
```

## 扩展规则

所有 "把文本丢进 JS 字符串" 的场景都要小心 —— 模板字符串 + 反引号、单引号里单引号、双引号里双引号。LLM system prompt 特别容易踩，因为 prompt 里常常有代码示例。

粗暴预防：**系统 prompt 用字符串拼接或数组 `join('\n')`，别一坨大 template literal**。
