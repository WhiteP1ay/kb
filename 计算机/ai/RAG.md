---
tags:
  - Agent
created: "2026-08-13"
---

# RAG（检索增强生成）

## 是什么

Retrieval Augmented Generation，开卷考试：先检索相关资料，塞进 prompt，再让模型基于资料回答。

## 关键点

- 三步：文档 embedding → 提问 embedding 检索 → 塞 prompt 生成
- 治「不知道」：私有数据 + 知识截止
- 不改模型，运行时喂资料
- 灵魂是 R（检索），没有检索只是「手动贴资料」

## 陷阱

- 和 fine-tune 别混：RAG 改输入，fine-tune 改模型
- 资料质量比模型更决定答案对不对

## 相关笔记

[[Embedding]] · [[Vector Database]] · [[Fine-tune]] · [[Hallucination]]
