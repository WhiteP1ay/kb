---
tags:
  - JavaScript
  - 面试
created: "2026-07-02"
---

# call、apply、bind 的区别

## 是什么

都用来改变 this 指向。call/apply 立即调用，bind 返回新函数（this 永久绑定）。call 逐参数，apply 数组传参。

## 关键点

- call(thisArg, arg1, arg2, ...)：逐个传参，立即调用
- apply(thisArg, [args])：数组传参，立即调用
- bind(thisArg)：返回新函数，this 永久绑定，后续 call/apply 无法覆盖
- call/apply 常用于方法借用：Array.prototype.slice.call(arguments)

## 相关笔记

[[this指向]]
