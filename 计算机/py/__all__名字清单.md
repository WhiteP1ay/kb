---
tags:
  - Python
created: "2026-08-31"
---

# __all__ 名字清单

## 是什么

包的对外接口清单，类型是 `list[str]`，装的是**名字（字符串），不是对象**。`import *` 时 Python 逐个字符串去模块全局命名空间里按名查对象。字符串是钥匙，对象是锁柜里的东西。

## 怎么用

```python
# shop/__init__.py
from .cart import Cart
from .pricing import apply_discount

__all__ = ["Cart", "apply_discount"]
```

## 关键点

- 消除 re-export 的 unused 警告：检查器看到名字出现在 `__all__` 里，视为「已被使用/故意导出」
- 控制 `from shop import *` 只导出列出的名字；没有 `__all__` 时会导出所有非下划线开头的名字（包括你自己 import 的 `math`、`os`）
- 读别人的包先看 `__all__`，一眼知道公开接口

## 陷阱

- 写的是字符串，不是对象：`__all__ = [Cart]` 是错的，要 `["Cart"]`
- `__all__` 只影响 `import *`，普通 `from shop import X` 不受限制

## 相关笔记

[[__init__.py与re-export]]
[[import机制]]
