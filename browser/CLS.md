---
tags:
  - 性能
  - Core Web Vitals
  - 浏览器
created: "2026-07-02"
---

# CLS（Cumulative Layout Shift）累积布局偏移

## 是什么

衡量页面视觉稳定性。越大元素偏移越远，CLS 越差。

layout shift score = impact fraction × distance fraction。

## 关键点

- 常见原因：图片无尺寸、动态插入广告、动画用 margin/top 触发重排
- 图片解决：`aspect-ratio: 16/9` + `object-fit: cover` 预留空间
- 广告：预留设好高度的容器（骨架屏）
- 动画：用 `transform` 替代 `margin-top`/`top`/`left`
- CLS ≤ 0.1 为良好
