---
tags:
  - 面试
  - JavaScript
  - Node
created: "2026-07-02"
---

# CommonJS 模块的本质（require 实现原理）

## 是什么

require 函数核心流程：解析路径 → 查缓存 → 执行模块代码（包裹在函数中注入 exports/module/require 等变量）→ 返回 module.exports。

## 关键点

- 缓存机制：同一个模块只会加载一次，moduleCache 存 module.exports
- 模块代码被包裹在函数中执行：function(exports, require, module, __filename, __dirname) { ... }
- module.exports 和 exports 初始指向同一个对象，但最终导出的是 module.exports
- 循环引用：返回的是截至当前已执行部分的 exports（不完整对象）

## 相关笔记

[[ESM-vs-CommonJS]]
