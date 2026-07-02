---
tags:
  - React
  - React19
created: "2026-07-02"
---

# React useEffectEvent（effect 依赖大救星）

## 是什么

让 effect 访问最新 props/state 而不需要把它们放入依赖数组，避免 effect 因非关联数据变化而重新执行。

## 关键点

- 问题：useEffect 中访问 user.name 必须放依赖数组→user.name 变化导致不必要重连
- 解决：`const onMsg = useEffectEvent((msg) => { console.log(user.name) })` → effect 依赖保持 [] 
- onMsg 总能拿到最新值，effect 只运行一次
- 减少不必要的 effect 重执行和 ESlint 报错
