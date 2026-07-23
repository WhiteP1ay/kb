---
tags:
  - JavaScript
  - 性能
created: "2026-07-02"
---

# Web Worker / SharedWorker / ServiceWorker 区别

## 是什么

三种 Worker 都在独立线程运行，不阻塞主线程。Web Worker 单页面专用，SharedWorker 多页面共享，ServiceWorker 网络代理层。

## 关键点

- Web Worker：专属于创建它的页面，页面关闭则终止。适合 CPU 密集计算
- SharedWorker：同源多个页面共享同一个实例，通过 port 通信
- ServiceWorker：独立的网络代理层，可拦截请求、缓存资源、离线推送。PWA 核心
- 共同限制：无法访问 DOM、无 window 对象、通过 postMessage 通信
- 典型场景：大文件哈希计算、图片处理、大量数据解析
