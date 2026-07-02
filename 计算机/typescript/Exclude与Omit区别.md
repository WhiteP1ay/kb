---
tags:
  - 面试
  - TypeScript
created: "2026-07-02"
---

# TypeScript Exclude 与 Omit 的区别

## 是什么

Exclude<T, U> 作用于联合类型（排除成员）；Omit<T, K> 作用于对象类型（删除属性）。

## 关键点

- `Exclude<'a'|'b'|'c', 'a'>` → `'b' | 'c'`
- `Omit<{a:1, b:2}, 'a'>` → `{b: 2}`
- 记法：Exclude 看联合类型「里面有什么」，Omit 看对象类型「少了什么」
- Omit 内部实现：`Pick<T, Exclude<keyof T, K>>`

## 相关笔记

[[TypeScript-never类型]]
