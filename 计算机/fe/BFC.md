---
tags:
  - CSS
  - 布局
created: "2026-07-02"
---

# BFC（块级格式化上下文）

## 是什么

BFC 是一个独立的渲染区域，内部元素的布局不会影响外部。常用来解决 margin 折叠、清除浮动、防止高度塌陷。

## 关键点

- 触发 BFC：`overflow: hidden/auto/scroll`、`display: flow-root`、`float` 非 none、`position: absolute/fixed`
- `display: flow-root` 是最干净的触发方式——不引入其他副作用
- 解决 margin 折叠：父子或兄弟间 margin 重叠时，给父元素触发 BFC
- 清除浮动：BFC 容器会自动包含浮动子元素（高度塌陷问题）
- BFC 不会与浮动元素重叠——可用来做两栏自适应布局
