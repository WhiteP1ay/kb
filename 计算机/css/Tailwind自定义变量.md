---
tags:
  - 面试
  - CSS
  - Tailwind
created: "2026-07-02"
---

# Tailwind CSS 自定义常用变量

## 是什么

在 tailwind.config.js 的 theme.extend 中配置自定义值，如 fontSize、colors、spacing 等，不覆盖默认值。

## 关键点

- `theme.extend.fontSize` 中配：`'1r': ['1rem', '1.5']` → 数组第一个是字号，第二个是行高
- 配置后模板中直接用 `text-1r`、`text-2r`
- extend 不会覆盖默认值，是在默认值基础上追加

## 相关笔记

[[shadcn主题实现]]
