---
tags:
  - Vue
  - 面试
created: "2026-07-02"
---

# Vue 3 defineModel API

## 是什么

Vue 3.4+ 引入的语法糖，简化父子组件双向绑定

## 关键点

- 默认 prop 是 modelValue，事件是 update:modelValue
- 可自定义：`const value = defineModel('value')`
- 内部自动生成 prop 声明和对应事件处理，省去手动写 props 和 emits

## 相关笔记

[[Vue3-Teleport]]
