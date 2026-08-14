---
tags:
  - Python
created: "2026-08-13"
---

# Python 语法坑：从 JS 迁移

## 是什么

JS 老手迁 Python 的高频语法坑清单。多数已单独成卡，本卡做导航 + 补新坑。

## 已单独成卡的坑

- `!`/`&&`/`||` → `not`/`and`/`or`（[[and-or-not]]）
- 链式比较 `a < b < c` 是 AND 区间语义，JS 无同款（[[成员检查与链式比较]]）
- `^` 是异或不是乘方，乘方是 `**`（[[乘方与异或]]）
- `==` 值比较 vs `is` 身份比较（[[相等与身份比较]]）

## 新增坑

- **`++` 不存在**：自增只有一种写法

```python
x += 1      # 唯一自增写法
++x         # 合法但啥也不干：+(+x) 一元正号嵌套
x++         # SyntaxError
```

- **f-string** = JS 模板字符串

```python
f"¥{x}"         # ≈ `¥${x}`
f"{x:.2f}"      # 保留两位小数
```

- **三元表达式**：`cond ? A : B` → `A if cond else B`（条件放中间，结果 A 在前）

```python
x = 1000 if unit == "ms" else 1   # JS: const x = unit === "ms" ? 1000 : 1
```

- **`NotImplemented` ≠ `NotImplementedError`**

```python
return NotImplemented        # 特殊值：交还 Python 尝试反向比较
raise NotImplementedError    # 异常：「方法未实现」，抽象基类占位
```

- **魔术方法名不能发明**：`__lt__`/`__le__`/`__gt__`/`__ge__` 查表；拼错（`__lg__`）→ 静默失效

## 相关笔记

[[and-or-not]]
[[成员检查与链式比较]]
[[乘方与异或]]
[[相等与身份比较]]
[[魔术方法总览]]
