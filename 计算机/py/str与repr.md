---
tags:
  - Python
created: "2026-08-13"
---

# __str__ 与 __repr__：字符串表示

## 是什么

两个「对象打印出来长什么样」的协议，观众不同：

- `__str__`：给人类看（`print(obj)`）
- `__repr__`：给开发者看（REPL、日志、容器打印）

## 怎么用

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __str__(self):
        return f"({self.x}, {self.y})"

    def __repr__(self):
        return f"Point({self.x!r}, {self.y!r})"
```

- 惯例：`__repr__` 信息完整，最好长得像能重建对象的代码
- `!r` 是 f-string 格式符，等价 `repr(x)`，字符串值带引号

## 关键点

- 没写 `__str__` 时 print 退回 `__repr__`；反向不行
- **容器（列表/dict）打印元素用 `__repr__`，不是 `__str__`**
- `__str__` 必须返回字符串，返回 int 报 `TypeError: __str__ returned non-string`
- f-string = JS 模板字符串：`f"¥{x}"` ≈ `` `¥${x}` ``；`f"{x:.2f}"` 保留两位小数

## 陷阱

- 只写 `__str__` → 对象放进列表打印还是 `<Point object at 0x...>`
- 生产代码 `__repr__` 比 `__str__` 更重要（日志/REPL/容器全找它）

## 相关笔记

[[魔术方法总览]]
