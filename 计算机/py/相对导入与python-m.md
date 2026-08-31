---
tags:
  - Python
created: "2026-08-31"
---

# 相对导入与 python -m

## 是什么

相对导入用点号表示包层级：`.` 当前包、`..` 父包、`...` 祖父包。它只能在**包内部**的模块之间用，直接运行的入口文件不能用（没有父包上下文）。

## 怎么用

```python
from .cart import Cart        # 包内：从当前包导入
from ..core import db         # 包内：从父包导入
from app.services import user_service   # 绝对导入：从顶层包路径导入
```

```bash
python shop/demo.py    # 脚本模式：报 ImportError: no known parent package
python -m shop.demo    # 模块模式：有包上下文，相对导入可解析
```

## 关键点

- 铁律：被直接运行的那个入口文件，永远用绝对导入
- `-m` 是告诉 Python「当模块运行，不是当脚本」，包上下文就有了
- 报错 `no known parent package` 是「`.` 无法解析」，不是「名字找不到」——炸的是点号，根本没走到查名字那步

## 陷阱

- 直接运行的文件 `__name__` 是 `"__main__"`，没有父包，`.` 无从指起
- 相对导入报错先检查运行方式：是不是忘了 `-m`

## 相关笔记

[[__name__双身份]]
[[__init__.py与re-export]]
[[import机制]]
