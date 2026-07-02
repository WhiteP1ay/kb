---
tags:
  - CSS
  - 布局
created: "2026-07-02"
---

# CSS 定位方案

## 是什么

五种定位：static（默认）、relative（相对自身偏移）、absolute（相对包含块）、fixed（相对视口）、sticky（滚动粘滞）。

## 关键点

- static：默认值，正常文档流，top/left/z-index 无效
- relative：相对自身原位置偏移，原空间保留，不脱离文档流
- absolute：脱离文档流，相对最近的定位祖先（position 非 static）
- fixed：脱离文档流，相对视口，不随滚动移动
- sticky：相对定位和固定定位的混合——滚动到阈值前正常流，之后 fixed
- sticky 失效常见原因：父容器 overflow:hidden、未指定 top 等阈值

## 相关笔记

[[包含块]]
