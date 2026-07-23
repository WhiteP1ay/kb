---
tags:
  - CSS
  - 性能
  - 浏览器
created: "2026-07-02"
---

# will-change 属性

## 是什么

告诉浏览器「这个元素即将变化」，浏览器提前创建独立合成层做优化。
但滥用会导致 GPU 内存爆增。

## 关键点

- 用法：`will-change: transform` 或 `will-change: opacity`
- 浏览器提前为元素创建独立合成层，变化时不用重新 Layout/Paint
- 滥用风险：每个合成层占 GPU 内存，全加 will-change→内存爆炸
- 最佳实践：变化前动态添加，变化完成后移除
- 常见误区：不要写在静态样式里，不要对所有动画元素加
