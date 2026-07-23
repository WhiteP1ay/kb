---
tags:
  - React
  - React19
created: "2026-07-02"
---

# React 19.2 Activity 组件（keep-alive）

## 是什么

`<Activity>` 是 React 19.2 内置组件，控制不可见部分的行为——类似 Vue 的 keep-alive。隐藏时清理 effect 但保留状态，切换回来渲染成本更低。

## 关键点

- vs 条件渲染：条件渲染卸载/挂载→状态丢失、effect 重跑；Activity 保持状态
- vs display:none：Activity 隐藏时自动 cleanup effect，更省资源
- 隐藏时保持低优先级活跃，浏览器空闲时静默更新 UI
- 适用于 Tab 切换——不再需要重新获取数据，不丢失滚动位置
