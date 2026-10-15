---
tags:
  - Python
created: "2026-08-13"
---

# dict 取值用方括号

## 是什么

Python 从 dict 取值用方括号 + 字符串 key：

```python
o["amount"]    # 正确
o.amount       # 错误：Python 的 . 是取属性，dict 没有 amount 属性
```

## 陷阱

JS 肌肉记忆：`obj.amount` 在 JS 里顺手，切到 Python 会高频复发。这是从 JS 转 Python 的最高频踩坑点之一。

```python
o["amount"]        # dict 取值
o.get("amount")    # 安全取值：key 不存在返回 None 而不是报错
```

## 关键点

- Python 对象（类实例）才用 `.`，dict 一律用 `[]` 或 `.get()`
- `result.get(uid, 0)` 这种带默认值的写法在聚合场景是标配

## 相关笔记

[[字典推导式]] · [[推导式的边界]]
