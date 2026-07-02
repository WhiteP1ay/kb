---
tags:
  - 工程化
  - Nginx
created: "2026-07-02"
---

# Nginx 核心概念

## 是什么

高性能 HTTP 和反向代理服务器。事件驱动+少量 worker 进程处理万级并发。核心场景：反向代理、负载均衡、静态资源、SSL 终结。

## 关键点

- 正向代理：代理客户端（科学上网）；反向代理：代理服务器端（统一入口）
- 负载均衡：轮询、加权轮询、IP Hash、动静分离
- 配置三大块：全局块 → events块 → http块（upstream + server + location）
- SPA 关键配置：`try_files $uri $uri/ /index.html`
- reload 不中断服务：新 worker 加载新配置，旧 worker 优雅退出
