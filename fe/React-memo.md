---
tags:
  - React
  - 性能
  - 面试
created: "2026-07-02"
---

# React.memo

## 是什么

高阶组件，对 props 做浅比较（Object.is），props 不变则跳过重渲染。第二个参数可自定义比较函数。但滥用反而降低性能——只在纯展示组件或重渲染代价高的场景使用。

## 关键点

- 签名：`React.memo(Component, arePropsEqual?)`
- 默认比较函数是 Object.is 逐 prop 比较
- 只针对 props 变化做优化——useState/useContext/useReducer 引起的状态变化 memo 管不了
- **不应该默认包裹所有组件**：memo 本身有比较开销，props 频繁变化的组件加了反而更慢
- 第二个参数可自定义比较函数：`React.memo(Comp, (prev, next) => prev.id === next.id)`
- 配合 useCallback/useMemo 传递稳定的 props 引用才能真正发挥效果
- 判断标准：组件重渲染代价高 + props 变化不频繁 → 用 memo

## 相关笔记

[[React-useDeferredValue]] [[Object.is-vs-严格相等]]
