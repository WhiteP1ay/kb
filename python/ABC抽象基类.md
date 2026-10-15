---
tags:
  - Python
created: "2026-08-31"
---

# ABC 抽象基类

## 是什么

接口契约的运行时强制。`class Shape(ABC)` 让「子类必须实现某方法」从运行时惯例变成创建时强制。等于 TS 的 abstract class + interface 合体，但 TS 是编译期检查，Python 是**运行时**检查（实例化时报 TypeError）。

## 怎么用

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self) -> float: ...

    def describe(self) -> str:        # 具体方法，子类直接继承
        return f"面积是 {self.area()}"

class Circle(Shape):
    def __init__(self, r: float) -> None:
        self.r = r
    def area(self) -> float:
        return 3.14159 * self.r ** 2
```

## 关键点

- 唯一模板：继承 `ABC` + 抽象方法带 `@abstractmethod`，方法体 `...` 是占位不是实现
- **创建时炸，不是调用时炸**：`Circle(2.0)` 直接 `TypeError: Can't instantiate abstract class`，对象造不出来
- 抽象方法定义契约，具体方法定义共享逻辑（模板方法模式），子类一行不写直接用
- 与 mypy 是两条线：mypy 静态查类型，ABC 运行时查接口

## 陷阱

- 抽象类 = 不能实例化，不是不能调用；看到 `TypeError: Can't instantiate abstract class` 先想「有抽象方法没实现」
- `**` 是幂运算，`^` 是异或，别混

## 相关笔记

[[类的继承]]
[[类型标注与运行时]]
[[import机制]]
