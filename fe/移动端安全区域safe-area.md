---
tags:
  - CSS
  - 面试
created: "2026-07-02"
---

# 移动端安全区域 safe-area

## 是什么

全面屏手机（iPhone X+）有刘海、圆角、Home Indicator，内容贴边会被遮挡。CSS 的 env() 函数配合 safe-area-inset-* 变量来解决。

## 关键点

- `env(safe-area-inset-bottom, 20px)`：四个方向（top/right/bottom/left），第二个参数是 fallback
- 兼容写法：iOS 11.0-11.2 用 `constant()`，iOS 11.2+ 改用 `env()`，可同时写两行做兜底
- 这些值是浏览器自动提供的 CSS 环境变量，不是手动注入的

## 相关笔记

[[CSS-prefers-color-scheme]]
