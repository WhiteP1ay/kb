---
tags:
  - 面试
  - JavaScript
  - 基础
created: "2026-07-02"
---

# Object.is 与 === 的区别

## 是什么

Object.is 与 === 大多数情况相同，但有两个关键差异：能区分 +0 和 -0，且 NaN 等于自身。React.memo 默认用 Object.is 做比较。

## 关键点

- `Object.is(0, -0)` → false（=== 返回 true）
- `Object.is(NaN, NaN)` → true（=== 返回 false）
- 其余情况行为与 === 一致

## 相关笔记

[[React-memo]] [[双等号判断规则]]
