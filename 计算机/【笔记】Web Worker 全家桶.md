# 浏览器里的多线程：一文搞懂 Web Worker 全家桶

JavaScript 是一个"单线程"的语言。这句每个前端都听过的话，实际意味着什么？

打开一个网页，JS 在主线程上跑。渲染页面、响应用户点击、执行动画——所有这些事都挤在同一条流水线上。一旦某段 JS 算得太久（比如处理一个大 JSON、做图片滤镜、或者跑一个复杂循环），整条流水线就卡住了。按钮点不动、滚动不跟手、动画掉帧——用户看到的就是"页面卡死了"。

这就是 Web Worker 存在的意义。**它让你在主线程之外另起一条"分流水线"**，把重活丢给这条分流水线，主线程继续响应用户操作。下面我们把 Worker 家族逐个拆开看。

---

## Dedicated Worker：最基础的"分流水线"

Dedicated Worker（专用 Worker）是最朴素的形态。一个页面创建一个 Worker，这个 Worker 只服务这一个页面。

```javascript
// 主线程
const worker = new Worker('worker.js');

// 给 Worker 发消息
worker.postMessage({ type: 'calculate', data: hugeArray });

// 收 Worker 的回信
worker.addEventListener('message', (e) => {
  console.log('Worker 算完了:', e.data);
});
```

Worker 里没有 `window`，没有 `document`。你不能在里面操作 DOM——这其实是好事，因为它天然避免了和主线程抢 DOM 的问题。Worker 的全局对象是 `self`：

```javascript
// worker.js
self.addEventListener('message', (e) => {
  const result = heavyCalculation(e.data);
  self.postMessage(result);
});
```

如果需要引入其他脚本，Worker 里不能用 `<script>` 标签，而是用 `importScripts`：

```javascript
importScripts('helper.js', 'utils.js');
// 现在 helper 和 utils 里导出的东西可以直接用了
```

`importScripts` 是同步的，会按顺序加载完再往下执行。

---

## SharedWorker：让多个页面共用一条 Worker

Dedicated Worker 是"一对一"的关系——一个页面配一个 Worker。SharedWorker 则是"多对一"：**同源的多个标签页共享同一个 SharedWorker 实例**。

这有什么用？最简单的场景：一个音乐播放器，你开了两个标签页。如果用 SharedWorker 管理播放状态，两个标签页看到的状态是一致的——暂停就是真暂停，不用在每个页面各管各的。

```javascript
// 主线程（A 页面和 B 页面写一样的代码）
const worker = new SharedWorker('shared-worker.js');

// SharedWorker 通信需要通过 port，并且必须手动启动
worker.port.start();
worker.port.postMessage('hello from page A');

worker.port.addEventListener('message', (e) => {
  console.log('SharedWorker 回信:', e.data);
});
```

只要至少有一个同源页面还连着，SharedWorker 就活着；所有关联页面都关闭后，它才会被销毁。判断"是不是同一个"SharedWorker 的依据是**脚本路径**——A 页面和 B 页面必须用完全相同的 URL 来 `new SharedWorker()`，才会连到同一个实例。

---

## Service Worker：独立于页面的"常驻代理"

Service Worker 和前面两个有本质区别。它不依附于任何一个具体页面——即使所有页面都关了，它依然可以活着（浏览器会根据需要决定是否休眠）。

它的核心作用是**拦截网络请求**。你去访问一个 URL，Service Worker 可以在这个请求发出去之前截住它，然后决定：是从缓存里拿，还是继续发出去。这就是 PWA 离线可用的基础。

### 生命周期

Service Worker 有自己的"生命周期"，和页面生命周期完全是两回事：

1. **install**：第一次注册时触发。通常在这里预缓存关键资源（HTML、CSS、JS、首屏图片）
2. **activate**：安装完成后触发。同一个脚本再次注册不会重新 install，而是走 activate
3. **fetch**：当前端发出网络请求时触发——这是 Service Worker 最核心的拦截点

### 缓存资源

Service Worker 自带一个 `caches` 全局对象（不能用 `localStorage`），用来做缓存：

```javascript
// 预缓存关键资源
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open('v1').then((cache) => {
      return cache.addAll([
        '/',
        '/index.html',
        '/style.css',
        '/app.js',
      ]);
    })
  );
});

// 拦截请求，优先返回缓存
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((cachedResponse) => {
      return cachedResponse || fetch(event.request);
    })
  );
});
```

这就是经典的 Cache First 策略：先查缓存，命中直接返回；没命中才走网络。被缓存过的请求返回 304，浏览器知道不用重新下载。

### 更新控制

Service Worker 有自己的更新机制，两个关键 API：

- **skipWaiting()**：新安装的 Service Worker 不等待旧版释放，立即激活
- **clients.claim()**：激活后立即接管所有受控页面，不用等用户刷新

这两者通常搭配使用，让新版 Service Worker 无缝上线。

Service Worker 还有一个很实用的 API——`self.clients.matchAll()`，可以拿到当前受控的所有页面，用来做广播通知之类的操作。

---

## Node.js 里的 Worker：服务端的多线程

Node.js 这边也有 Worker，但动机完全不同。浏览器 Worker 是为了不卡 UI；Node.js 没有 UI，Worker 是为了**利用多核 CPU**。

Node.js 10+ 通过 `worker_threads` 模块引入：

```javascript
// 主线程
const { Worker } = require('worker_threads');

const worker = new Worker('./worker.js', {
  workerData: 40  // 直接把初始数据传进去
});

worker.on('message', (msg) => {
  console.log('子线程结果:', msg);
});
```

子线程里的写法也略有不同，需要从 `worker_threads` 解构出 `workerData` 和 `parentPort`：

```javascript
// worker.js
const { workerData, parentPort } = require('worker_threads');

let num = workerData; // 40
let result = fibonacci(num);
parentPort.postMessage(result);
```

和浏览器 Worker 的关键区别：Node.js Worker 可以传初始数据（`workerData`），而且通信路径是 `parentPort` 而非 `self`。

---

## 一张表总结

| 类型 | 作用域 | 生命周期 | 核心用途 |
|------|--------|----------|----------|
| Dedicated Worker | 单个页面 | 页面关闭即销毁 | 密集计算、数据处理 |
| SharedWorker | 同源多页面 | 所有关联页面关闭后销毁 | 多标签页共享状态 |
| Service Worker | 同源所有页面 | 独立于页面，浏览器管理 | 离线缓存、请求代理 |
| Node.js Worker | 单个进程 | 主线程结束即销毁 | CPU 密集型任务、多核利用 |

---

## 一个容易踩的坑

Worker 里拿不到 DOM，也拿不到 `window`。这不是 bug，是设计。初学者经常在 Worker 里写 `document.querySelector()` 然后纳闷为什么报错——如果你需要在 Worker 计算完后更新 DOM，正确的做法是把结果 `postMessage` 回主线程，让主线程去操作 DOM。

理解了这个"主线程—Worker 线程"的消息通信模型，Worker 的用法就通了。
