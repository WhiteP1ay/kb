---
tags:
  - JavaScript
  - Node.js
  - 面试
created: "2026-07-22"
---

# Node.js process.nextTick

## 是什么

`process.nextTick(callback)` 是 Node.js 提供的延迟执行机制。它会在当前 JavaScript 调用栈执行完毕后、事件循环继续进入下一个阶段前执行回调。

它不属于 V8 管理的标准微任务队列：

- `process.nextTick` 进入 Node.js 管理的 **next tick queue**
- `Promise.then` 和 `queueMicrotask` 进入 V8 管理的 **microtask queue**

## CommonJS 与 ESM 的不同表现

测试代码：

```javascript
console.log("A");

setTimeout(() => console.log("B"), 0);

Promise.resolve().then(() => console.log("C"));

process.nextTick(() => console.log("D"));

console.log("E");
```

### CommonJS 顶层

```text
A
E
D
C
B
```

通常可以理解为：

```text
同步代码 → next tick queue → microtask queue → timer
```

在 CommonJS 顶层以及一般的 Node.js 回调边界中，Node.js 会先清空 next tick queue，再清空 Promise 微任务队列。

### ES Module 顶层

```text
A
E
C
D
B
```

ES Module 的模块求值本身已经在微任务队列中执行。此时 Node.js 正在清空 microtask queue，因此已经排入的 `Promise.then` 会先执行，之后才处理 `process.nextTick`。

所以不能脱离运行上下文，简单断言 `process.nextTick` 永远比 `Promise.then` 更早执行。

以上两组结果在 Node.js v24.15.0 中实测通过。

## Legacy 状态

Node.js 官方将 `process.nextTick()` 的稳定性标记为 **Stability 3: Legacy**：

- Node.js v22.7.0
- Node.js v20.18.0

这里的 Legacy 不代表 API 已被删除，也不等同于立即不可用。官方建议：大多数用户代码优先使用跨 JavaScript 平台的 `queueMicrotask()`；只有确实需要 `process.nextTick()` 特性时再使用它。

## 注意：可能饿死 I/O

Node.js 会完整清空 next tick queue。递归添加 `process.nextTick` 回调，会让事件循环无法进入 timer、I/O 等后续阶段：

```javascript
function loop() {
  process.nextTick(loop);
}

loop();
```

这段代码的效果类似无限循环，会造成 I/O starvation。

## 参考资料

- [Node.js Process API：process.nextTick](https://nodejs.org/api/process.html#processnexttickcallback-args)
- [Node.js：queueMicrotask 与 process.nextTick](https://nodejs.org/api/process.html#when-to-use-queuemicrotask-vs-processnexttick)

## 相关笔记

[[ESM-vs-CommonJS]]
[[事件循环Event-Loop]]
[[微任务与宏任务]]
[[queueMicrotask]]