---
tags:
  - Python
created: "2026-07-30"
---

# Python for 循环

## 是什么

Python 的 `for` 循环会从一个可迭代对象中依次取出元素，并在每一轮把当前元素绑定给指定的名字。

```python
statuses = ["open", "closed", "unknown"]

for status in statuses:
    print(status)
```

输出：

```text
open
closed
unknown
```

它更接近 JavaScript 的 `for...of`，得到的是元素，而不是 `for...in` 得到的键或索引。

## 同时获得计数值和元素

`enumerate()` 是 Python 的内置函数，不是列表方法。它接收一个可迭代对象，返回一个会依次产生二元组的迭代器。

```python
statuses = ["open", "closed", "unknown"]

for index, status in enumerate(statuses):
    print(index, status)
```

`enumerate(statuses)` 会依次产生：

```python
(0, "open")
(1, "closed")
(2, "unknown")
```

`index, status` 会对每个二元组执行拆包：

```python
index, status = (0, "open")
```

等价于：

```python
index = 0
status = "open"
```

## start 参数

`start` 只改变 `enumerate()` 生成的计数值，不会改变元素在列表中的真实索引。

```python
names = ["Anna", "Bob"]

for position, name in enumerate(names, start=1):
    print(position, name)
```

输出：

```text
1 Anna
2 Bob
```

`"Bob"` 的真实列表索引仍然是 `1`，这里只是显示位置为 `2`。

## continue

`continue` 会立即跳过当前这一轮中剩余的代码，直接进入下一轮循环，语义与 JavaScript 相同。

```python
statuses = ["open", "closed", "unknown"]

for status in statuses:
    if status == "closed":
        continue

    print(f"检查：{status}")
```

输出：

```text
检查：open
检查：unknown
```

如果 `continue` 后面已经没有其他循环体代码，那么它通常是冗余的。

## 容易混淆的点

- Python 的 `for...in` 更接近 JavaScript 的 `for...of`。
- `enumerate()` 是内置函数，不是对象方法。
- `start=1` 改变的是计数起点，不是列表索引。
- `index, value` 是二元组拆包。
- `continue` 只跳过当前轮，不会结束整个循环。
- `print()` 输出字符串内容时，不会显示代码里的引号。

## 闭卷自测

```python
names = ["Anna", "Bob"]

for position, name in enumerate(names, start=1):
    print(position, name)
```

1. 程序输出什么？
2. `position` 和 `name` 分别来自哪里？
3. `"Bob"` 的真实索引和显示位置分别是多少？

## 相关笔记

- [[可迭代对象与迭代器]]
