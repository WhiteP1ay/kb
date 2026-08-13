---
tags: []
created: "2026-08-13"
---

# AI 术语地图

## 一句话锚点

LLM 的本质是「下一个 token 预测器」：喂一段文本，输出下一个最可能出现的 token，循环几百上千次就是「生成」。

## 四层结构

层一 · 模型怎么理解语言（底层机制）
[[Token]] → [[Embedding]]（语义坐标）→ [[Attention]]（谁重要）→ [[Transformer]]（架构骨架）

层二 · 模型怎么造出来
[[Pre-training]]（通识教育）→ [[Fine-tune]]（定向培训，改权重）

层三 · 怎么用
[[Prompt]]（输入）｜ [[Training vs Inference]]（训练 vs 推理）｜ [[Context Window]]（视野）｜ [[Temperature]]（随机性旋钮）

层四 · 补 LLM 三大缺陷
[[RAG]] 治「不知道」｜ [[Function Calling]] 治「只会说不会做」｜ [[Agent]] 治「要拆多步做决策」

## 三句口诀

RAG 治「不知道」——补知识，不改模型
Fine-tune 治「不会用那个姿势说」——改模型本身
Function calling 治「只会说不会做」——给它手

## 贯穿隐患

[[Hallucination]]：模型会一本正经编造，RAG 的「基于资料回答」正是为压它。
