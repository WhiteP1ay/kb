---
tags:
  - Python
created: "2026-08-13"
---

# @property：属性带守卫

## 是什么

把方法伪装成属性的协议：读走 getter，写走 setter（可校验）。内部存 `_amount`（下划线 = 私有约定），对外暴露 `amount`。

## 怎么用

```python
class Money:
    def __init__(self, amount):
        self._amount = amount

    @property
    def amount(self):
        return self._amount

    @amount.setter
    def amount(self, value):
        if value < 0:
            raise ValueError("金额不能为负")
        self._amount = value
```

外部 `m.amount` 读、`m.amount = 50` 写，跟普通属性一样，背后跑方法。校验逻辑集中在 setter 一处。

## 关键点

- JS 对应 class 的 `get amount()` / `set amount(value)`，几乎一一对应
- 底层机制是描述符协议（`__get__`/`__set__`）：类属性实现这对方法，实例访问被拦截
- SQLAlchemy `Column`、Pydantic 字段都是描述符

## 陷阱

- setter 里写 `self.amount = value` → 无限递归（赋值又触发 setter），必须写 `self._amount`
- 属性名要前后一致：property 名 = 外部接口名，内部存储加下划线

## 相关笔记

[[魔术方法总览]]
