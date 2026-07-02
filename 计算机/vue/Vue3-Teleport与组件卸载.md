---
tags:
  - 面试
  - Vue
  - 框架
created: "2026-07-02"
---

# Vue 3 Teleport 与组件卸载的关系

## 是什么

Teleport 把组件内容渲染到 DOM 任意位置（如 body），但它仍然是当前组件的子组件——只传送了 DOM 节点，没传送组件树的从属关系。

## 关键点

- 生命周期钩子仍由父组件控制
- 父组件卸载时，Teleport 里的内容也会被销毁
- 本质：DOM 位置 != 组件树位置

## 相关笔记

[[Vue3-defineModel]]
