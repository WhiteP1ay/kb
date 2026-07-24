---
tags:
  - JavaScript
  - 面试
created: "2026-07-02"
---

# JavaScript 退出循环的方法

## 是什么

五种：break（结束整个循环）、continue（跳过本次）、return（结束函数）、throw（抛异常）、some/every（逻辑条件后退出）。forEach 不能用 break/return 退出。

## 关键点

- break/continue：只对 for/while/do-while/switch 有效
- return：只能在函数内使用，直接退出函数
- throw：抛异常中断所有代码执行
- some()：回调返回 true 时停止迭代
- every()：回调返回 false 时停止迭代
- forEach 不可以用 break 或 return 退出循环！这是面试经典坑

## 相关笔记

[[闭包]]
