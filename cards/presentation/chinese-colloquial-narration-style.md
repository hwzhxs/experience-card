---
title: 中文 TTS 脚本 · 重口语化风格（ElevenLabs Jessica）
category: presentation
created: 2026-04-21
tags: narration, chinese, elevenlabs, voice, tts
---

## 原则

给 ElevenLabs（或任何高质量 TTS）写中文语音稿时，**写给耳朵听，不是给眼睛看**。默认用**重口语化**风格，像真人脱稿讲 deck。

## 5 种技法

| # | 技法 | 用途 | 例 |
|---|---|---|---|
| 1 | **引入语** | 开场 / 切换话题 | `好，`、`那，`、`嗯，` |
| 2 | **自问自答** | 制造节奏感、拉回注意力 | `那怎么办呢？`、`凭什么？`、`说明啥？` |
| 3 | **语气助词** | 句尾柔化 | `呢`、`嘛`、`吧`、`啊` |
| 4 | **二人称** | 拉近距离 | `你看`、`你想想`、`咱们` |
| 5 | **轻停顿** | 模拟思考 | `说白了`、`其实`、`这个` |

## 节制原则

- **不堆砌** — 每页 2-4 处人类化
- **每种技法用 1-2 次**，不重复
- **关键数据/观点不加口语词**（不稀释重点）
- 听起来像"一边想一边讲"，不是"演讲稿"

## ElevenLabs 配置

```python
VOICE_ID = "cgSgspJ2msm6clMCkdW9"  # Jessica - Playful, Bright, Warm
MODEL = "eleven_v3"
VoiceSettings(stability=0.25, similarity_boost=0.85, speed=1.7)
# language_code="zh"
```

## 纯文本 vs SSML

ElevenLabs 不支持 SSML。**标点自然表达停顿**：
- `，` 短停顿
- `。` 句末
- `——` 明显停顿（破折号）
- `？` 带升调
- `！` 带情绪

不要写 `{p}` `{pp}` 标记（那是 Azure SSML 用的）。

## 数字读法

中文数字读出来：
- `0.73%` → `百分之零点七三`
- `23%` → `百分之二十三`
- `230` → `两百三十`

## 反例（太书面）vs 正例（口语化）

**反例**：
> Mobile Nudge 怎么做的？所有长文章都弹同一个问题，要不要帮你总结。看这个漏斗，有两个问题。

**正例**：
> Mobile Nudge 现在是怎么做的呢？很简单，所有长文章都弹同一个问题——要不要帮你总结一下。那效果怎么样呢？你看这个漏斗，其实有两个问题。

**改动**：加了 `呢` 自问、`很简单` 引入、`——` 破折号、`那...呢？` 过渡、`你看` 二人称、`其实` 轻停顿。

## 源头

Edge Mobile Copilot Truth deck Nudge 专题 5 页 (2026-04-21)。主 deck slide 0-29 已经是这风格（ElevenLabs Jessica），Nudge 专题新 5 页迭代成同风格。
