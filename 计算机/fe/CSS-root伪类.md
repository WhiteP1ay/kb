---
tags:
  - 面试
  - CSS
created: "2026-07-02"
---

# CSS :root 伪类

## 是什么

`:root` 代表 HTML 的根元素（`<html>` 标签），但优先级高于直接写 `html {}`。通常用来定义全局 CSS 变量。

## 关键点

- 适合作为主题变量定义入口：`--primary-color`、`--font-size-base`
- 优先级高于 html 选择器
- 在 SVG 等非 HTML 文档中，:root 也能正确指向根元素

## 相关笔记

[[shadcn主题实现]] [[CSS-has选择器]]
