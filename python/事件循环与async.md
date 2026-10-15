---
tags:
  - Python
created: "2026-09-09"
---

# 事件循环与 async

## 是什么

事件循环是单线程协作式调度器：同一时刻只跑一个协程，协程主动让出后才轮到下一个。Python 的 asyncio 是标准库框架，**必须显式启动**（`asyncio.run`）；JS 的事件循环是浏览器/Node 宿主内置的，你感觉不到它——这是两个语言 async 最大的差异。

心智模型（奶茶店比喻）：一个店员（单线程）服务多个客人。协程 = 客人，`await` 真异步操作 = 下单后说「好了叫我」，店员转头服务下一个；`time.sleep` = 抱住店员不放，全场陪等。

## 关键点

- **调用 async 函数不执行**：`c = fetch()` 只造 coroutine 对象，函数体冻结（RuntimeWarning: was never awaited）。调度它才开工：`asyncio.run()` / `await` / `asyncio.gather()` / `create_task()` 四选一。
- **await 是开工令，也是让出点**：`await` 只能在 `async def` 里写（语法硬规则）；`async def` 可以没有 await 但无意义（没有让出点 = 同步代码白套壳）。
- **阻塞 vs 让出**：`time.sleep`/同步库调用卡死整个事件循环（伪异步实验：async def + gather + time.sleep = 照样串行）；`asyncio.sleep`/异步 IO 才让出。async 治 IO 密集，不治 CPU 密集。
- **完成顺序 = 等待完成时间**，不是代码顺序。A 等 2s B 等 1s，B 先完成。
- **开工令 ≠ 完工等待**：`gather(...)` 调用那刻任务已启动，但它返回的 future 要 `await` 才等齐。`main` 跑完事件循环就关，没被等的任务全部 CancelledError。

## JS vs Python：创建即启动 vs 创建不启动

| | JS | Python |
|---|---|---|
| `const p = a()` / `c = a()` | promise 创建即启动，函数体跑到第一个 await | coroutine 创建不启动，函数体冻结 |
| 并发取决于 | promise 创建时机 | 协程调度时机（gather/create_task） |
| 先创建后统一 await | 并发（两个都在跑） | **串行**（逐个 await 才逐个启动） |

JS 老手的「先创建 promise 再 await」并发惯用法搬进 Python 会无声退化成串行。

## 陷阱（实测踩坑）

- `main()` 里写 `asyncio.gather(...)` 忘 `await`：任务全启动，但没人等，main 结束事件循环关闭，任务被取消（CancelledError）。gather 要 `await asyncio.gather(...)`。
- `asyncio.run(main())` 等的是 main 完成，不是所有任务完成——main 里没等完的活它不管。
- async 函数里用同步库（requests、psycopg2、time.sleep）＝伪异步，并发场景性能全丢。

## 相关笔记

[[语法坑-从JS迁移]]
[[可迭代对象与迭代器]]
