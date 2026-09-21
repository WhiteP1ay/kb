---
tags:
  - FastAPI
created: "2026-09-21"
---

# FastAPI 参数推导规则

## 是什么

FastAPI 根据路由路径中的占位符、函数形参类型以及是否有默认值，自动将请求数据解析并归类为 **Path**、**Query** 或 **Body**。

## 怎么用

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class ItemCreate(BaseModel):
    title: str


@app.get("/items/{item_id}")
def get_item(
    item_id: int,  # 1. Path 路径参数：在 URL 中有 {item_id}
    skip: int = 0,  # 2. Query 查询参数：不在 URL 中，有默认值（选填）
    token: str,  # 3. Query 查询参数：不在 URL 中，无默认值（必填！）
):
    return {"item_id": item_id, "skip": skip, "token": token}


@app.post("/items/")
def create_item(payload: ItemCreate):  # 4. Body 请求体：类型为 BaseModel 子类
    return payload
```

## 关键点

- **三界推导法则**：
  * **Path 参数**：名字在路径 `{...}` 模板中出现。
  * **Query 参数**：标量类型（`int`/`str`/`bool` 等）且名字不在路径中。有默认值则选填，无默认值则必填。
  * **Body 请求体**：继承自 Pydantic `BaseModel` 的复合对象，自动解析 HTTP 请求体 JSON。
- **422 错误定位（loc）**：
  校验失败时统一返回 HTTP 422，`loc` 数组精准指出层级：
  * 路径参数错：`loc: ["path", "item_id"]`
  * 查询参数错：`loc: ["query", "token"]`
  * 请求体字段错：`loc: ["body", "title"]`

## 陷阱

- **占位符命名不一致**：路径写 `@app.get("/items/{item.id}")` 而形参写 `item_id`，因名字未精确匹配，FastAPI 会把 `item_id` 降级为 Query 参数（`?item_id=...`），导致路径匹配 404。
- **Swagger UI 前端自校验拦截**：Swagger UI 会在浏览器端阻止非法表单提交；测试 422 校验边界应使用 `curl`、`fetch` 或 `pytest` 直接发请求。

## 相关笔记

[[FastAPI与Uvicorn]] · [[Pydantic与响应模型]]
