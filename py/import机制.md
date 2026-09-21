---
tags:
  - Python
created: "2026-08-31"
---

# import 机制

## 是什么

`import` 是**普通语句，不是声明**。执行到那一行才生效，放函数里、if 里都行。顶部导入只是 PEP 8 惯例，不是语法要求。ESM 的 import 语法强制在顶层，Python 完全不同。

import 会**执行被导入文件的全部代码**（副作用），然后结果缓存在 `sys.modules` 字典里，模块一生只加载一次。

## 怎么用

```python
def f():
    from b import B   # 延迟导入：用到时才导入，b 早已加载完，拿缓存
    return B()
```

## 关键点

- 第二次 import 不再执行文件，直接从 `sys.modules` 拿缓存
- 延迟导入安全的原因：等函数真正被调用时，对方模块早就加载完了
- 被导入文件的顶层代码（print、全局语句）在 import 时就会执行

## 陷阱

- 顶层副作用代码在导入时执行：测试 print 会污染导入方的输出，用 `if __name__ == "__main__":` 守卫（见 [[__name__双身份]]）
- 顶层互相 import 会循环（见 [[循环导入]]）

## 相关笔记

[[循环导入]]
[[__name__双身份]]
[[__init__.py与re-export]]
[[ABC抽象基类]]
