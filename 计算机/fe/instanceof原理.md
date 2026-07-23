---
tags:
  - 面试
  - JavaScript
  - 基础
created: "2026-07-02"
---

# instanceof 的实现原理

## 是什么

沿 obj.__proto__ 链逐个比对，看是否与 Constructor.prototype 匹配——就是沿着原型链一路往上找。

## 关键点

- 核心逻辑：while(proto) { if(proto === Constructor.prototype) return true; proto = proto.__proto__ }
- 基本类型直接返回 false
- 手写实现只需 ~10 行代码
- 注意：跨 iframe/window 的 instanceof 会失效（不同执行环境有不同的 Object.prototype）

## 相关笔记

[[原型链]] [[new操作符原理]]
