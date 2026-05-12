---
title: 语音脚本一页一页改 · 在聊天里用 table 迭代，最后一次写入文件
category: presentation
created: 2026-04-21
tags: narration, audio, tts, workflow
---

## 原则

写 deck 语音脚本（TTS 配音）时，**不要直接改 Python 脚本文件**。应该：
1. 在聊天里以 table 形式展示 `页 # | 标题 | 语音文案`
2. 用户一页一页提反馈（改措辞、节奏、过渡）
3. 所有页定稿后**一次性写入** `generate-audio-*.py` SCRIPTS dict
4. 跑 TTS 生成 MP3

## 为什么

- **文案需要反复打磨**：用户看到 V1 会说"太书面、太啰嗦、加过渡"，中途改 Python 文件浪费 edit
- **Table 展示可对比**：4-5 页并排看，风格一致性容易发现
- **写入文件和跑 TTS 是 atomic 操作**：一次做完，不要碎片化

## Table 格式

```markdown
| # | 页标题 | 语音文案（SSML 或纯文本）|
|---|---|---|
| 30 | xxx | 好，{p}那么接下来，... |
| 31 | xxx | Mobile Nudge 现在... |
```

SSML pause marker (`{p}`, `{pp}`, `{ppp}`) 或纯文本（ElevenLabs 用标点即可）。

## 迭代节奏

- 每页给**2-3 个候选**或一个草稿让用户选
- 用户说"改哪句"就改哪句，改完 table 更新
- 每次改完**只展示这页**，不重复整张 table
- 结束后再给汇总 table

## 反面教训

早期会直接 edit `generate-audio-azure.py`，结果每改一句要：
1. 编辑 Python 文件
2. 跑完整 script 生成所有 35 页 MP3（浪费 ~10 分钟）
3. 用户听完说再改
4. 重复

改用 table 迭代后：
- 文案定稿 = 写入 = 单次 TTS 调用（只跑改的页）

源头：Mobile Nudge 专题 5 页语音脚本迭代 (2026-04-21)
