---
tags:
  - LLM
created: "2026-08-13"
---

# Training vs Inference（训练与推理）

## 是什么

训练 = 改权重（开发阶段，烧钱烧卡）；推理 = 权重冻结，纯调用（运行时）。前端类比：训练 = build，推理 = 运行时调 API。

## 关键点

- 用 ChatGPT 是在 inference，模型不在「学习你的话」
- 权重冻结：对话不会让模型变得更懂你

## 陷阱

- 以为用得久模型就更懂自己——错，除非把历史塞进下一次 context window

## 相关笔记

[[Fine-tune]] · [[Context Window]]
