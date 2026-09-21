---
tags:
  - Python
created: "2026-08-13"
---

# __call__：实例可调用

## 是什么

让实例能当函数用的协议：`obj()` 触发 `__call__`。适合「带状态的函数」——状态放 `__init__`，行为放 `__call__`。

## 怎么用

```python
class Greeter:
    def __init__(self, prefix):
        self.prefix = prefix

    def __call__(self, name):
        return f"{self.prefix}, {name}!"

hi = Greeter("你好")
hi("朴昊哲")   # '你好, 朴昊哲!'
```

## 关键点

- 区别普通方法：方法要 `obj.method()`，`__call__` 是 `obj()` 本身
- JS 类比：函数即对象可挂属性；Python 用 `__call__` 更显式
- 装饰器内部常用（带配置的回调）
- 类可调用（`Class()` 造实例 = `type` 的 `__call__`）≠ 实例可调用（本类的 `__call__`）

## 陷阱

- 忘写 return → 调用返回 None（print 输出 None，同 JS 函数无 return 返回 undefined）
- `__call__` 签名只有 self，没有 other

## 相关笔记

[[魔术方法总览]]
[[对象创建两阶段]]
