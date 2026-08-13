---
tags:
  - Agent
created: "2026-08-13"
---

# Vector Database（向量数据库）

## 是什么

存 embedding 向量的数据库，做「语义相似度搜索」。前端类比：不是精确匹配查，而是按「意思接近」查。

## 关键点

- RAG 的检索层，存文档向量
- 代表：Pinecone / Weaviate / Milvus / pgvector
- 查询用余弦相似度 / 内积

## 陷阱

- 存的是向量不是原文，召回原文要另存映射

## 相关笔记

[[Embedding]] · [[RAG]]
