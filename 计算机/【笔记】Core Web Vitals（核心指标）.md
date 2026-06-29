优化前，要先学会‘体检’，才能知道项目问题出在哪。

体检结果就是Core Web Vitals（核心指标），目前是由三项组成 LCP + INP + CLS。

我们可以按F12，打开performance页签看到这三个指标。

### LCP(Largest Contentful Paint) 最大内容绘制

https://web.dev/articles/lcp

指视窗内最大的可见元素（通常是大图、视频或大段文本）完全渲染出来需要的时间。

![lcp](https://web.dev/static/articles/lcp/image/good-lcp-values.svg?hl=zh-cn)

工作原理：浏览器会持续监听屏幕上出现的元素，不断更新“当前最大的那个元素”是谁，直到用户开始交互。一旦用户进行了交互（点击、滚动、按键），或者页面被隐藏，浏览器就会停止寻找，并把**最后一次**确定的最大元素的渲染时间记为 LCP 时间。

常见优化策略：

大图压缩后使用、用更友好的格式（webp）、提高下载优先级（fetchpriority="high"）、图片放cdn托管等

考虑使用SSR。因为SSR可以让图片、或别的什么内容第一时间呈现。否则都要等待js加载完毕才会绘制。

关于下载优先级 https://developer.mozilla.org/zh-CN/docs/Web/HTML/Reference/Attributes/fetchpriority

关于预加载方案后边会具体讲解。

### INP (Interaction to Next Paint)下次绘制交互

https://web.dev/articles/inp

指用户与页面进行交互（点击按钮、输入文字等）到浏览器通过下一帧绘制做出视觉反馈的延迟时间。

![inp](https://web.dev/static/articles/inp/image/inp-desktop-v2.svg?hl=zh-cn)

工作原理：INP 会监测页面生命周期内**所有**的交互，然后取**耗时最长**的那次交互时间作为最终分数。

INP主要分三块，分别是输入延迟时间、处理时间、和呈现延迟。

：比如鼠标点击某个按钮，首先会有一个输入延迟，因为JS是单线程，主线程可能有别的任务，所以不能第一时间响应操作。即从用户手指按下，到 JS 事件回调函数真正开始运行之前的时间是输入延迟时间。

处理时间：事件回调函数的执行所需时间是处理时间。

呈现延迟：代码执行完了，浏览器需要重新计算样式、布局并把像素画到屏幕上（Paint）。这段时间叫呈现延迟。

如果你之前了解过性能指标，可能听说过FID (First Input Delay)。

INP 已于 **2024年3月** 正式取代了旧指标 FID (First Input Delay)。INP 比 FID 更严格，它监测页面**整个生命周期**内的所有交互延迟，而不仅仅是第一次。

### CLS (Cumulative Layout Shift)累积布局偏移

https://web.dev/articles/cls

用来衡量视觉稳定性。

```
layout shift score = impact fraction * distance fraction
```

简单来说就是越大的元素，布局时被挤占偏移的距离越远，该指标越差。

![cls](https://web.dev/static/articles/cls/image/good-cls-values.svg?hl=zh-cn)

常见的原因

1. 图片元素没定义尺寸，图片加载的慢，等加载出来了整个文档都要偏移。如果宽高不好确定可以考虑如下写法。

```css
.card-image {
  width: 100%;
  /* 锁定 16:9 宽高比 */
  aspect-ratio: 16 / 9; 
  /* 确保图片不拉伸 */
  object-fit: cover;
}
```

浏览器确定宽高比可以始终预留高度。避免文档偏移。

2. 动态插入的内容，比如一些网站为了盈利植入了Google AdSense。广告插入的时候导致元素偏移。正确方法是给广告预留一个设置了高度的容器（骨架屏）。

3. 错误的使用动画，比如使用`margin-top` 或 `top/left` 来做动画。每次位移都会触发重排。浏览器在每一帧都要重新计算布局，从而产生偏移。正确方法是用`transform: translateY()`。