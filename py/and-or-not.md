---
tags:
  - Python
created: "2026-07-31"
---

# and、or 与 not

## 是什么

`and`、`or` 和 `not` 是 Python 的逻辑运算符：

```text
and：逻辑与
or：逻辑或
not：逻辑非
```

它们对应 JavaScript 中的 `&&`、`||` 和 `!`，但 Python 和 JavaScript 对空容器的真值判断不同。

## 短路求值

`and` 和 `or` 从左到右求值，并在结果已经确定时停止，不再计算右侧表达式。

```python
ready = False

ready and print("不会执行")
ready or print("会执行")
```

这种行为叫短路求值（short-circuit evaluation）。这里用 `print()` 是为了展示求值行为；真实业务中的条件副作用通常应使用清晰的 `if`，不要为了省一行而滥用逻辑表达式。

## or 返回什么

`or` 返回第一个真值操作数；如果所有操作数都是假值，返回最后一个操作数。

```python
print("" or "匿名用户")     # 匿名用户
print([] or ["fallback"])  # ['fallback']
print("ready" or "backup") # ready
```

## and 返回什么

`and` 遇到假值时返回该假值；如果所有操作数都是真值，返回最后一个操作数。

```python
print("" and 42)       # 空字符串
print("ready" and 42)  # 42
```

`and` 和 `or` 返回的是操作数本身，不保证返回 `bool`。

## not 返回什么

`not` 会先判断对象真值，然后返回相反的布尔值，所以结果一定是 `True` 或 `False`。

```python
print(not [])      # True
print(not [1, 2])  # False
```

## 默认值陷阱

下面的写法会把所有假值都当成“缺少值”：

```python
count = 0
result = count or 10

print(result)  # 10
```

如果 `0` 是合法值，而只有 `None` 表示缺少值，就应该明确判断：

```python
result = 10 if count is None else count
```

同样的陷阱也适用于空字符串、空列表和空字典。

## 容易混淆的点

- `and` 和 `or` 会短路求值。
- `and` 和 `or` 返回操作数，不一定返回 `bool`。
- `not` 一定返回 `bool`。
- `value or default` 会把 `0`、空字符串和空容器一并视为假值。
- Python 空容器是假值，与 JavaScript 不同。

## 闭卷自测

```python
print(0 or 100)
print("" or "unknown")
print("ready" and 42)
print([] and "执行")
print(not {})
```

1. 五行分别输出什么？
2. 如果 `0` 是合法业务值，为什么不应该无条件使用 `value or default`？

## 相关笔记

- [[真值规则|Python 真值规则]]
- [[相等与身份比较]]
