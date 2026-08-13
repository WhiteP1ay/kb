---
tags:
  - Python
created: "2026-08-13"
---

# __eq__ 与 __hash__：值相等

## 是什么

让自定义类的 `==` 按值比较的协议。默认 `==` 比内存地址（同 JS `===`）。dict/set 的键查找 = `__hash__` 定位 + `__eq__` 确认，两步。

## 怎么用

```python
def __eq__(self, other):
    if not isinstance(other, Money):
        return NotImplemented
    return self._value == other._value

def __hash__(self):
    return hash(self._value)   # 与 __eq__ 用同一字段
```

## 关键点

- 定义 `__eq__` 后 `__hash__` 自动变 None → 不可哈希 → `TypeError: unhashable type`
- **铁律**：相等的对象哈希必须相同 → 两者基于同一字段；多字段用 `hash((a, b))`
- 可变对象别实现 `__hash__`：字段改了哈希就变，dict 里存进去找不回
- 内建类型（list 等）`==` 已按值比较；自定义类才需要 `__eq__`
- `@dataclass(frozen=True)` 自动生成两者

## 陷阱

- `__hash__` 里写 `hash(self)` → 无限递归 RecursionError（`hash(x)` 会调用 x 类型的 `__hash__`）
- `NotImplemented`（return，交还 Python 尝试反向比较）≠ `NotImplementedError`（raise，未实现异常）
- 只实现 `__eq__` 忘 `__hash__` → 进不了 set / dict 键

## 相关笔记

[[魔术方法总览]]
[[相等与身份比较]]
