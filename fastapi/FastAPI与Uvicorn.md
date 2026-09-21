---
tags:
  - FastAPI
created: "2026-09-21"
---

# FastAPI 与 Uvicorn

## 是什么

FastAPI 与网络底层服务器遵循解耦的 **ASGI（异步服务器网关接口）** 架构：
* **FastAPI**：应用层框架，负责路由分发、类型注解解析、依赖注入与请求处理。
* **Uvicorn**：专职高性能 ASGI 服务器，底层基于 Node 同款的 **uvloop**（libuv 的 Python 封装）与 **httptools**（C 语言 HTTP 解析器），专职在 OS 层面监听 TCP 端口并收发网络数据包。

## 怎么用

最小可运行服务：

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def root():
    return {"message": "Hello World"}
```

启动命令（通过 uv 运行当前虚拟环境的 uvicorn）：

```bash
uv run uvicorn main:app --reload
```
* `main`：文件名（`main.py`）
* `app`：代码中实例化的 FastAPI 变量名
* `--reload`：热重载模式（代码保存即生效）

## 关键点

- **代码即文档**：启动后访问 `/docs` 自动生成 Swagger UI，访问 `/redoc` 自动生成 ReDoc，数据源均为 `/openapi.json`。
- **无 `app.listen()`**：与 Node/Express 不同，Python 框架不捆绑 HTTP 服务器，启动入口交由命令行 Uvicorn 接管。

## 陷阱

- **`async def` 内写同步阻塞**：若在 `async def` 路由里调用 `time.sleep(5)` 或同步数据库 IO，会直接卡死主事件循环。
- **普通 `def` 的妙用**：如果有不可避免的同步阻塞操作，直接声明为普通函数 `def root():`，FastAPI 会自动丢进内部独立线程池运行，不会阻塞主事件循环。

## 相关笔记

[[事件循环与async]] · [[FastAPI参数推导规则]]
