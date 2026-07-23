---
tags:
  - React
  - 状态管理
created: "2026-07-02"
---

# useSyncExternalStore（解决并发撕裂）

## 是什么

React 18 并发渲染下，外部 store 可能在「同一轮渲染」里被不同组件读到不同版本（撕裂）。
useSyncExternalStore 让 React 在渲染阶段同步读取快照来保证一致性。

## 关键点

- 旧方案 useEffect+useState：订阅在 effect 里（渲染提交后），渲染期间 store 变化无法感知
- useSyncExternalStore 三个参数：subscribe、getSnapshot、getServerSnapshot
- 渲染时同步读快照→store 变了 React 发现快照过期→重来整轮渲染
- 代价是多渲染一次，收益是消除撕裂
- Zustand、Redux 等库底层依赖它
