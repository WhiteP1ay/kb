---
tags:
  - 面试
  - JavaScript
  - 工程化
created: "2026-07-02"
---

# ESM 与 CommonJS 的区别

## 是什么

CommonJS 同步加载（require），运行时确定，值的拷贝。ESM 静态（编译时确定），支持异步加载，值的引用，tree-shaking 友好。

## 关键点

- CJS：require 运行时加载，module.exports 导出，导出值的拷贝
- ESM：import 编译时静态分析，export 导出，导出值的引用（活绑定）
- ESM 支持 tree-shaking（因为静态结构），CJS 不行
- Node.js 中 .mjs 或 package.json type:"module" 启用 ESM
- 循环引用：CJS 返回不完整对象，ESM 通过活绑定解决

## 相关笔记

[[CommonJS本质]]
