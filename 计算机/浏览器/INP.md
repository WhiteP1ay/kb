---
tags:
  - 性能
  - Core Web Vitals
created: "2026-07-02"
---

# INP（Interaction to Next Paint）交互响应

## 是什么

用户交互（点击、输入）到浏览器下一帧视觉反馈的延迟。
2024年3月正式取代 FID，监测页面整个生命周期所有交互延迟。

## 关键点

- INP = 输入延迟 + 处理时间 + 呈现延迟
- 取耗时最长的那次交互作为最终分数
- 输入延迟：JS 单线程，主线程忙时无法立刻响应
- 优化：拆分长任务（scheduler.yield）、减少主线程占用、用 Web Worker 搬走重计算
- INP ≤ 200ms 为良好
