---
tags:
  - CSS
  - 面试
created: "2026-07-02"
---

# shadcn 的主题实现原理

## 是什么

基于 data-theme 属性和 CSS 变量的主题系统——组件只引用变量名，切换 html 上的 data-theme 即可切换整套配色。

## 关键点

- `:root` 定义明亮模式变量（如 --background），`[data-theme="dark"]` 覆盖为暗色值
- 组件引用同一套变量名（`var(--background)`），不关心当前是什么主题
- 核心优势：组件与主题解耦——换主题不需要改组件代码

## 相关笔记

[[CSS prefers-color-scheme]] [[CSS :root伪类]]
