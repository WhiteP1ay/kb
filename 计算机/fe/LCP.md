---
tags:
  - 性能
  - Core Web Vitals
  - 浏览器
created: "2026-07-02"
---

# LCP（Largest Contentful Paint）最大内容绘制

## 是什么

视窗内最大可见元素（大图、视频、大段文本）完全渲染出来的时间。
衡量加载性能的核心指标。

## 关键点

- 浏览器持续监听最大元素直到用户交互或页面隐藏
- 优化：大图压缩、WebP 格式、fetchpriority="high"、CDN托管
- SSR 可让首屏内容第一时间呈现，不等 JS 加载
- LCP ≤ 2.5 秒为良好
