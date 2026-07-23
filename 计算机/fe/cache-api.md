---
tags:
  - React
  - React19
  - SSR
created: "2026-07-02"
---

# React cache() API

## 是什么

`cache()` 是 React 的**请求级记忆化**函数——同一个渲染周期内，相同参数调用只执行一次，直接返回缓存结果。专为服务端组件设计。

## 关键点

- 用法：`const getPost = cache(async (id) => fetch(...))` → 同一请求中多次调用只发一次 fetch
- 作用域：**单次渲染请求**（不是跨请求，不是全局缓存）——每个 HTTP 请求独立
- 场景：多个组件需要同一份数据时，不必层层传递或各自请求——cache 自动去重
- 与 `React.memo` 区别：memo 缓存组件渲染结果（跨渲染），cache 缓存函数调用结果（单次渲染内）
- 与 `useMemo` 区别：useMemo 是组件级 hooks，cache 可在组件外任意位置使用
- React 19.2 新增 `cacheSignal()` 配合：当 cache 失效时自动取消内部 fetch，避免浪费

## 为什么重要：从框架垄断到能力共享

以前全栈框架（Next.js、Remix）的请求去重是**框架私有的黑盒**——各自在背后维护请求级 Map，各自处理 AbortController。一套逻辑每个框架自己造一遍，第三方数据库无法复用。

`cache()` + `cacheSignal()` 把这件事从框架层**抽到 React 层**：

- **去重**：`cache()` 声明式搞定，不依赖 Next.js 的魔改 fetch，任何库都能用
- **取消**：`cacheSignal()` 天然绑在渲染生命周期上，React 知道渲染中断后自动发信号

这意味着请求层缓存不再是「Next.js 的 fetch 比原生 fetch 强」，而是「用 React 的 cache() 包一下，原生 fetch 就有请求级去重」。**能力从框架垄断变成了 React 生态共享**——Remix、TanStack Start 等其他全栈方案更容易在数据层追上 Next.js，而不需要各自造一套私有缓存实现。

## 相关笔记

[[cacheSignal]]
