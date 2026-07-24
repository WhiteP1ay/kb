---
tags:
  - JavaScript
  - 面试
created: "2026-07-02"
---

# 如何让对象支持 for...of 迭代

## 是什么

为对象实现 Symbol.iterator 方法，返回一个迭代器对象（或用 generator 函数简化）。Generator 的 yield 自动处理迭代器协议。

## 关键点

- `obj[Symbol.iterator] = function*() { ... }`
- Generator 的 yield 自动处理迭代器协议（value + done）
- 普通对象默认不可迭代（没有 Symbol.iterator）
- Array、Map、Set、String 等内置可迭代对象都有默认 Symbol.iterator

## 相关笔记

[[Proxy-vs-defineProperty]]
