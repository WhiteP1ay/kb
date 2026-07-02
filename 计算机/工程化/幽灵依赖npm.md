---
tags:
  - 面试
  - 工程化
  - npm
created: "2026-07-02"
---

# npm 幽灵依赖（Phantom Dependency）

## 是什么

npm v3+ 扁平化 node_modules 导致项目可以访问未在 package.json 中声明的包——它是其他依赖的子依赖被提升到了顶层。

## 关键点

- 原因：npm 扁平化 node_modules，依赖的依赖被提升到顶层
- 风险：依赖 A 更新后不再依赖 B，你的代码里 import B 就炸了
- pnpm 通过硬链接+符号链接+非扁平 node_modules 彻底解决
- 面试关键：能说出「能访问但未声明」就是幽灵依赖

## 相关笔记

[[Monorepo子包依赖管理]]
