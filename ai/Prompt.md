---
tags:
  - LLM
created: "2026-08-13"
---

# Prompt（提示词）

## 是什么

喂给模型的输入指令，是控制输出的一等公民手段（零成本改行为）。

## 关键点

- few-shot（少样本示例）能稳定输出格式 / 风格
- 结构化输出靠 JSON schema / function calling 的 schema 约束

## 陷阱

- 别把 prompt 能解决的（格式 / 风格）升级成 fine-tune

## 相关笔记

[[Fine-tune]] · [[Function Calling]]
