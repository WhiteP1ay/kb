---
tags:
  - 面试
  - JavaScript
  - 事件循环
created: "2026-07-22"
---

# 微任务与宏任务

## 事件循环模型

```
宏任务 → 清空所有微任务 → 渲染(浏览器) → 下一个宏任务
```

每一轮事件循环从一个宏任务开始，执行过程中产生的微任务会在当前轮次全部清空，然后才进入下一个宏任务。

## 宏任务 (Macrotask)

- `setTimeout` / `setInterval`
- I/O 回调
- UI 渲染事件 (浏览器)
- `setImmediate` (Node.js)
- `MessageChannel`

## 微任务 (Microtask)

- `queueMicrotask(fn)` — ES2020 标准 API
- `Promise.resolve().then()` / `.catch()` / `.finally()`
- `MutationObserver` (浏览器)
- `process.nextTick` (Node.js，独立于微任务队列的 nextTick 队列)

## 关键规则

1. 微任务队列 FIFO，同轮次新注册的微任务追加到队尾，本轮一并清空
2. 微任务在"同一个宏任务内"执行完毕，不会跨宏任务
3. 微任务中无限注册微任务 → 主线程永远阻塞，其他宏任务饿死
4. Node.js 中 process.nextTick 队列通常先于 Promise 微任务清空（ESM 顶层例外）

## 经典执行顺序

```javascript
console.log(1);
setTimeout(() => console.log(2), 0);
queueMicrotask(() => console.log(3));
new Promise(resolve => { console.log(4); resolve(); })
  .then(() => console.log(5));
console.log(6);
// 输出: 1 4 6 3 5 2
```

分析：同步代码 `1 4 6` → 微任务队列清空 `3 5` → 下一个宏任务 `2`。

Promise 构造器内的代码是同步执行的，只有 then 回调才进入微任务队列。

## 相关笔记

[[queueMicrotask]] [[process.nextTick]] [[事件循环Event-Loop]]
