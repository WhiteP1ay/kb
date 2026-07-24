---
tags:
  - JavaScript
  - Node.js
  - 面试
created: "2026-07-22"
updated: "2026-07-22"
---

# queueMicrotask

## 是什么

`queueMicrotask(callback)` 是 ES2020 标准 API，将回调显式加入微任务队列。回调在当前同步代码执行完毕后运行，早于任何 `setTimeout` 等宏任务。浏览器和 Node.js 都原生支持。

```javascript
console.log("A");
queueMicrotask(() => console.log("B"));
console.log("C");
// 输出: A C B
```

## 与 Promise.then 的对比

两者共用同一个微任务队列，调度时机完全等效。区别在于语义和错误处理：

| 特性 | queueMicrotask | Promise.then |
|------|---------------|-------------|
| 调度时机 | 微任务队列，等效 | 微任务队列 |
| 错误处理 | 回调抛错直接冒泡到全局 | 抛错被 Promise 兜住 → rejected promise |
| 语义意图 | "我要一个微任务" | 暗示异步链式操作 |
| 返回隔离 | 无返回值，不可链式调用 | 返回 Promise，可能被意外 then |

当不需要链式调用、也不需要 catch 兜底错误时，`queueMicrotask` 语义更清晰，也避免了 Promise 返回值被意外消费的风险。

## 统一回调执行时机

下面函数的问题：缓存命中时同步执行回调，未命中时异步执行回调，调用者难以预判执行时机：

```javascript
function getUser(callback) {
  if (cache.has("user")) {
    callback(cache.get("user"));  // 同步
    return;
  }
  fetchUser().then(callback);     // 异步
}
```

`queueMicrotask` 统一为始终异步：

```javascript
function getUser(callback) {
  if (cache.has("user")) {
    queueMicrotask(() => callback(cache.get("user")));
    return;
  }
  fetchUser().then(callback);
}
```

## 微任务队列执行规则

- FIFO 顺序
- 同一轮次内新注册的微任务追加到当前队列尾部，本轮一并清空
- 微任务中无限递归注册微任务 = 主线程永远阻塞（等效于 await 死循环）

```javascript
function loop() {
  queueMicrotask(loop);
}
loop();
// 定时器和 I/O 永远得不到执行
```

## 事件循环中的位置

```
宏任务 → 清空所有微任务(包括queueMicrotask) → 渲染(浏览器) → 下一个宏任务
```

```javascript
console.log(1);
setTimeout(() => console.log(2), 0);
queueMicrotask(() => console.log(3));
new Promise(resolve => { console.log(4); resolve(); })
  .then(() => console.log(5));
console.log(6);
// 输出: 1 4 6 3 5 2
// 同步(1,4,6) → 微任务队列(3,5) → 宏任务(2)
```

## 注意

Node.js 中 `process.nextTick` 进入独立的 nextTick 队列，**通常**优先于 Promise/queueMicrotask 微任务执行，但 ESM 顶层例外。详见 [[process.nextTick]]。

## 相关笔记

[[process.nextTick]] [[微任务与宏任务]] [[事件循环Event-Loop]]
