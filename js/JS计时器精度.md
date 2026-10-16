---
tags:
  - JavaScript
  - 面试
created: "2026-07-02"
---

# JavaScript 计时器为什么不能精确计时

主线程被同步代码阻塞时，计时器回调永远排队等。
`setTimeout(fn, 0)` 不是立即执行，要等同步代码跑完。

## 代码实现

rAF 高精度（适合视觉更新）

`requestAnimationFrame` 天然 ~16ms（60fps）一帧，配合时间差计算，比任何 `setTimeout` 都精确。

注意点：浏览器切后台时定时器会被降频，rAF 会被暂停。无解，属于系统级限制。

```js
function rafCountdown(totalMs, onTick, onDone) {
  const start = performance.now();
  let lastRemaining = totalMs;

  function frame() {
    const elapsed = performance.now() - start;
    const remaining = Math.max(0, totalMs - elapsed);

    // 只在数值变化时回调，避免无意义渲染
    if (remaining !== lastRemaining) {
      onTick(remaining);
      lastRemaining = remaining;
    }

    if (remaining > 0) {
      requestAnimationFrame(frame);
    } else {
      onDone?.();
    }
  }

  requestAnimationFrame(frame);
}
```


## 相关笔记

[[事件循环Event-Loop]]
[[requestAnimationFrame]]
