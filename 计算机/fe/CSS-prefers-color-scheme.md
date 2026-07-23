---
tags:
  - 面试
  - CSS
created: "2026-07-02"
---

# CSS @media prefers-color-scheme

## 是什么

系统级暗黑模式检测的 CSS 媒体查询，读取用户操作系统主题设置，被动响应式地切换样式。

## 关键点

- `@media (prefers-color-scheme: dark) { ... }` → 系统暗色时自动应用
- 与 shadcn 手动切换 data-theme 区别：这是被动响应式的，不需要 JS
- 用户改系统主题 → 页面自动变化

## 相关笔记

[[shadcn主题实现]]
