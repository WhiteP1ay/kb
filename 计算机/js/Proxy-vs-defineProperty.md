---
tags:
  - 面试
  - JavaScript
  - Vue
created: "2026-07-02"
---

# Proxy 与 Object.defineProperty 的区别

## 是什么

Object.defineProperty 是单属性 get/set；Proxy 代理整个对象，可拦截 13 种操作。Vue 3 用 Proxy 替代 Vue 2 的 defineProperty 正是因为这些限制。

## 关键点

- defineProperty：只能 get/set，无法拦截新增/删除属性、数组索引变化
- Proxy：13 种拦截（get、set、has、deleteProperty、ownKeys、apply、construct 等）
- Vue 2 → Vue 3 升级动机之一：defineProperty 需要递归遍历所有属性，且无法侦听数组索引和 length 变化
- Proxy 功能更全但性能较低；defineProperty 更轻量但局限大

## 相关笔记

[[Vue3-defineModel]]
