---
tags:
  - 面试
  - JavaScript
  - 事件循环
created: "2026-07-02"
---

# JavaScript 计时器为什么不能精确计时

## 是什么

JS 计时器永远无法精确——硬件（石英晶振漂移）+ 单线程事件循环（回调被阻塞）双重限制。`setTimeout(fn, 1000)` 表示「至少 1000ms 后」。

## 关键点

- 硬件层：石英晶振受温压影响有漂移；RTC 芯片靠纽扣电池维持；NTP 网络校准
- JS 层：主线程被同步代码阻塞时，计时器回调永远排队等
- 精度场景：用 rAF + 时间差计算，不依赖计时器回调次数
- `setTimeout(fn, 0)` 不是立即执行——也要等同步代码跑完

## 代码实现

### 方案一：自校正 setTimeout

不依赖回调触发次数，每轮根据实际经过的时间算出剩余延迟：

```js
function preciseCountdown(totalMs, onTick, onDone) {
  const start = performance.now();

  function tick() {
    const elapsed = performance.now() - start;
    const remaining = totalMs - elapsed;

    if (remaining <= 0) {
      onTick(0);
      onDone?.();
      return;
    }

    onTick(remaining);

    // 目标间隔 100ms，减去本轮实际多花的时间来修正
    const drift = elapsed % 100;
    const nextDelay = 100 - drift;
    setTimeout(tick, nextDelay);
  }

  tick();
}

// 用法：倒数 5 秒
preciseCountdown(
  5000,
  (remaining) => console.log((remaining / 1000).toFixed(1) + 's'),
  () => console.log('done')
);
```

核心：不假定上轮 `setTimeout` 刚好过了 100ms，而是用 `performance.now()` 算出真正过了多久，动态修正下一轮的延迟。

### 方案二：rAF 高精度（适合视觉更新）

`requestAnimationFrame` 天然 ~16ms（60fps）一帧，配合时间差计算，比任何 `setTimeout` 都精确：

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

### 为什么比直接用 setInterval 好

```js
// ❌ 差：漂移叠加。每次回调延迟 2ms，10 次后误差 20ms，100 次后 200ms
setInterval(() => { count--; updateUI(); }, 1000);

// ✅ 好：用时间差计算，误差不叠加
// 每次读 performance.now()，漂移被 reset 掉
```

### 面试要点

- `setTimeout(fn, delay)` 只是「最早执行时间」，不是精确的
- 精确计时器的核心是**不依赖回调触发次数**，用 **时间戳差值** 来计算
- 浏览器切后台时定时器会被降频（Chrome 最小 1000ms），rAF 也暂停——无解，属于系统级限制

## 相关笔记

[[事件循环Event-Loop]] [[requestAnimationFrame]]
