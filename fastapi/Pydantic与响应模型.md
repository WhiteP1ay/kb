---
tags:
  - FastAPI
created: "2026-09-21"
---

# Pydantic 与响应模型

## 是什么

Pydantic 是 FastAPI 的核心数据引擎，兼具 TypeScript 类型与 Zod 运行时校验的双重能力。配合路由的 **`response_model`**，实现严格的出参白名单安全过滤。

## 怎么用

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class UserCreate(BaseModel):
    username: str
    password: str


# 出参模型：只声明公开字段
class UserResponse(BaseModel):
    id: int
    username: str


@app.post("/users/", response_model=UserResponse)
def create_user(payload: UserCreate):
    # .model_dump() 将 Pydantic 实例转为纯字典
    db_record = {
        "id": 101,
        "password_hash": f"hash_{payload.password}",  # 敏感内部字段
        **payload.model_dump(),  # 解构合并
    }
    # FastAPI 会依据 response_model 自动过滤掉 password_hash
    return db_record
```

## 关键点

- **出参白名单过滤**：即使内部字典中包含 100 个敏感或冗余字段，只要 `response_model` 中未声明，FastAPI 在序列化返回前端时就会全部自动剥离。
- **字典与模型转换**：
  * 模型转字典：`data_dict = payload.model_dump()`
  * 字典解构合并（类似 JS `{ ...payload, id }`）：`{"id": 1, **payload.model_dump()}`
  * 原地合入（类似 JS `Object.assign(target, source)`）：`target_dict.update(new_payload.model_dump())`

## 陷阱

- **Pydantic 不是普通 dict**：直接写 `payload["id"] = 1` 会报 `TypeError`（模型不支持项赋值）；点号 `payload.username` 用于读写模型字段。
- **`update` 时必须转字典**：`target_dict.update(payload)` 会失败，必须写 `target_dict.update(payload.model_dump())`。

## 相关笔记

[[FastAPI参数推导规则]] · [[dict取值方括号]]
