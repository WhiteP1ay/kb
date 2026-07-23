---
tags:
  - 面试
  - React
  - 框架
created: "2026-07-02"
---

# React useDeferredValue Hook

## 是什么

延迟更新某个值的渲染，优先处理紧急更新（如用户输入），避免大数据量渲染阻塞交互。本质是可中断的优先级调度。

## 关键点

- `const deferredQuery = useDeferredValue(query)` → deferredQuery 滞后更新
- 新输入到来时，旧的滞后更新会被取消（可中断渲染）
- 适合与 useMemo 配合：大数据列表的渲染不阻塞用户输入
- 与 debounce 区别：debounce 是延迟执行，useDeferredValue 是优先级调度

## 相关笔记

[[React-memo]]
