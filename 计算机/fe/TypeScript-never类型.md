---
tags:
  - 面试
  - TypeScript
created: "2026-07-02"
---

# TypeScript never 类型

## 是什么

never 表示「永远不可能出现的值」——函数抛错、无限循环、switch default 穷举检查。

## 关键点

- 函数抛错：`function throwError(): never { throw new Error() }`
- 无限循环：`function loop(): never { while(true) {} }`
- switch default 穷举检查：`const _exhaustive: never = shape` → 漏分支就编译报错
- 与 void 区别：void 是函数正常执行结束但无返回值，never 是永远不会执行结束

## 相关笔记

[[Exclude与Omit区别]]
