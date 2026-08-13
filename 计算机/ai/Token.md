---
tags:
  - LLM
created: "2026-08-13"
---

# Token（词元）

## 是什么

LLM 读写文本的原子单位。文本被切分成一个个 token，模型以 token 为单位预测下一个。所谓生成，就是循环预测下一个 token。

## 关键点

- API 按 token 计费（input + output 分开算）
- 中文约 1 字 ≈ 1 token，英文约 4 字符 ≈ 1 token
- context window 用 token 计数

## 陷阱

- 多模态下 token 不是「词」，是图像 patch / 音频片段
- 同一个词在不同模型的分词结果可能不同，token 数不一样

## 相关笔记

[[Embedding]] · [[Context Window]]
