---
tags:
  - 面试
  - JavaScript
  - 基础
created: "2026-07-02"
---

# Boolean 与 boolean 的区别

## 是什么

boolean 是原始类型（true/false）；Boolean 是内置对象构造函数，通常作为普通函数做类型转换而非创建包装对象。

## 关键点

- Boolean('') → false；Boolean('hello') → true（作为转换函数）
- 不推荐 new Boolean(true) → 创建的是对象，if 判断永远是 truthy
- Boolean 作为转换函数是常见且安全的用法

## 相关笔记

[[双等号判断规则]]
