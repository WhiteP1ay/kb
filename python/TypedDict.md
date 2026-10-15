---
tags:
  - Python
created: "2026-08-25"
---

# TypedDict

## 是什么

dict 的 interface（PEP 589）：给 dict 的形状定义类型，让一长串 `list[dict[str, int | float]]` 变成可读、可复用的名字。

## 怎么用

```python
from typing import TypedDict

class Order(TypedDict):
    amount: int
    price: float

def total_price(orders: list[Order]) -> float:
    return sum(o["amount"] * o["price"] for o in orders)
```

等于 TS 的：

```ts
interface Order {
  amount: number;
  price: number;
}
```

## 关键点

- 运行时还是普通 dict，访问用 `o["amount"]`，不是 `o.amount`（点号访问是 dataclass 的事，见 [[类和对象]]）
- 好处：形状可读可复用，mypy/IDE 能抓拼错的 key（`o["amunt"]` 直接报错）
- `class Order(TypedDict)` 是继承特殊基类（见 [[类的继承]]）

## 陷阱

- 字段用 `name: str` 声明（类型标注），不是 `name = str`（那是赋值默认值）
- TypedDict 只做静态形状检查，不做运行时校验——`o["missing"]` 照样 KeyError

## 相关笔记

[[类型标注与运行时]]
[[容器类型标注]]
[[类的继承]]
[[dict取值方括号]]
[[类和对象]]
