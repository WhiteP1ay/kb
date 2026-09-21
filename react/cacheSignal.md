---
tags:
  - React
created: "2026-07-02"
---

# React cacheSignal（取消无效请求）

## 是什么

专为服务器组件设计——告诉 cache() 何时失效，让用户可以取消掉已不需要的操作，如过久的 fetch。

## 关键点

- 场景：用户 fetch 数据期间离开页面→请求仍在后台运行浪费资源
- 用法：`const signal = cacheSignal()`，传给 fetch 的 signal 参数
- cache 失效时自动取消对应请求
- 配合 `cache()` 使用，专为 RSC 设计

## 相关笔记

[[cache-api]]
