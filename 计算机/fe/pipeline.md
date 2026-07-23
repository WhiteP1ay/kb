---
tags:
  - 面试
  - JavaScript
  - Node.js
  - Stream
created: "2026-07-22"
---

# Node.js Stream pipeline

## 是什么

`pipeline()` 用于连接多个 Node.js Stream，将上游产生的数据依次传递给下游，并统一处理背压、错误传播和资源关闭。

```text
Readable → Transform → Writable
```

例如压缩文件：

```javascript
import { createReadStream, createWriteStream } from "node:fs";
import { pipeline } from "node:stream/promises";
import { createGzip } from "node:zlib";

await pipeline(
  createReadStream("input.txt"),
  createGzip(),
  createWriteStream("input.txt.gz"),
);
```

## 大文件 HTTP 传输

不能使用 `readFile()` 一次性读取 5GB 文件，否则可能造成内存溢出。应该边读边写：

```javascript
import { createReadStream } from "node:fs";
import { stat } from "node:fs/promises";
import { pipeline } from "node:stream/promises";

app.get("/download", async (req, res, next) => {
  try {
    const filePath = "./large-file.zip";
    const fileInfo = await stat(filePath);

    res.setHeader("Content-Type", "application/zip");
    res.setHeader("Content-Length", fileInfo.size);
    res.setHeader(
      "Content-Disposition",
      'attachment; filename="large-file.zip"',
    );

    await pipeline(createReadStream(filePath), res);
  } catch (error) {
    next(error);
  }
});
```

内存中只保留少量缓冲数据，内存占用主要由 Stream 的 `highWaterMark` 决定，而不是文件总大小。

## 背压 Backpressure

当下游消费速度跟不上上游生产速度时，需要通知上游暂停生产：

```text
磁盘读取速度快
→ 用户网络速度慢
→ HTTP 写入缓冲区积压
→ 暂停读取文件
→ 缓冲区腾出空间后继续
```

如果手动使用 Writable Stream：

```javascript
const canContinue = writable.write(chunk);

if (!canContinue) {
  readable.pause();

  writable.once("drain", () => {
    readable.resume();
  });
}
```

- `write()` 返回 `false`：内部缓冲区达到阈值，应暂停写入
- `drain`：缓冲区已经腾出空间，可以继续写入

忽略背压会让数据不断堆积在内存中，可能导致 GC 压力、内存增长甚至 OOM。

## pipeline 与 pipe

```javascript
readable.pipe(transform).pipe(writable);
```

`pipe()` 能处理基本的数据传输和背压，但多个 Stream 的错误处理、销毁和完成状态需要额外协调。

`pipeline()` 会将整条链视为一个整体：

- 自动协调背压
- 将错误传递给调用方
- 出错时销毁相关 Stream
- 可以等待整条链完成
- Promise 版本便于和 `async/await` 配合

因此生产代码中通常优先使用：

```javascript
import { pipeline } from "node:stream/promises";

await pipeline(source, transform, destination);
```

## 注意

- `pipeline()` 出错时可能销毁 HTTP Socket，响应头发出后不一定还能返回 JSON 错误
- 应在开始传输前完成文件存在性、权限等可预先执行的检查
- 客户端中断连接时，应及时停止文件读取和释放资源
- 下载服务可以结合 HTTP Range 实现断点续传

## 参考资料

- [Node.js Stream API](https://nodejs.org/api/stream.html)
- [Node.js：Backpressuring in Streams](https://nodejs.org/en/learn/modules/backpressuring-in-streams)

## 相关笔记

[[事件循环Event-Loop]]
