---
tags:
  - FastAPI
created: "2026-09-21"
---

# FastAPI 自动化测试

## 是什么

FastAPI 官方标准的接口自动化测试方案，由 **pytest** 测试框架与 **fastapi.testclient.TestClient** 配合组成（等价于前端生态的 Vitest / Jest + Supertest）。

## 怎么用

在 `test_main.py` 中编写用例：

```python
from fastapi.testclient import TestClient
from main import app, db_fake

client = TestClient(app)


# 夹具：每个测试函数执行前清理全局状态
def setup_function():
    db_fake.clear()


def test_create_and_get():
    # 1. 测试正常创建
    res = client.post("/todos", json={"title": "Test", "completed": False})
    assert res.status_code == 200
    assert res.json()["title"] == "Test"

    # 2. 测试 404 边界
    res_not_found = client.get("/todo/999")
    assert res_not_found.status_code == 404

    # 3. 测试 422 校验拦截
    res_bad = client.post("/todos", json={})
    assert res_bad.status_code == 422
```

执行测试命令：

```bash
uv run pytest test_main.py
```

## 关键点

- **无网络开销**：无需先运行 Uvicorn，也不需要监听任何本地端口。`TestClient` 基于 ASGI 协议在内存中直接调用应用函数，速度极快（几百毫秒跑完全部用例）。
- **状态与响应断言**：
  * 状态码校验：`assert res.status_code == 200`
  * JSON 响应体验证：`assert res.json()["id"] == 1`

## 陷阱

- **测试间状态污染**：在内存测试时，前一个测试插入的数据会留在全局变量中污染下一个测试。务必使用 `setup_function()` 或 pytest fixture 在用例前清空测试数据。

## 相关笔记

[[FastAPI与Uvicorn]] · [[FastAPI参数推导规则]]
