---
tags:
  - Python
created: "2026-08-31"
---

# __init__.py 与 re-export

## 是什么

包 = 一个带 `__init__.py` 的目录。`import 包` 等于**执行这个包的 `__init__.py`**。`__init__.py` 里 re-export 子模块的内容，外面只认包这一个入口，等于 ESM 的 barrel file（`index.ts` re-export）。

## 怎么用

```python
# shop/__init__.py
from .cart import Cart
from .pricing import apply_discount

__all__ = ["Cart", "apply_discount"]
```

```python
# main.py
from shop import Cart, apply_discount   # 不用知道内部文件结构
```

## 关键点

- `import shop` 执行 `__init__.py` 后，`shop` 命名空间里就有 `Cart` 了
- 好处：调用方只认识入口，内部文件改名随便改
- 配 `__all__` 消除 unused 警告（见 [[__all__名字清单]]）

## 陷阱

- 静态检查器不认识 re-export 意图，`from .cart import Cart` 会被报 unused，用 `__all__` 声明意图
- 目录里没有 `__init__.py` 就不是包（Python 3.3+ 有 namespace package 但行为不同，别依赖）

## 相关笔记

[[__all__名字清单]]
[[import机制]]
[[相对导入与python-m]]
