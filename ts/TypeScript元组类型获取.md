---
tags:
  - 面试
created: "2026-07-02"
---

# TypeScript 元组元素类型获取

## 是什么

用索引访问类型 `T[number]` 获取元组所有可能索引对应的元素类型，合并为联合类型。

## 关键点

- `type MyTuple = [string, number, boolean]`
- `MyTuple[number]` → `string | number | boolean`
- T[number] 是 TypeScript 中索引访问类型的常用技巧

## 相关笔记

[[TypeScript-never类型]]
