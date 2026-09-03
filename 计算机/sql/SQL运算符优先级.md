---
tags:
  - SQL
created: "2026-09-03"
---

# SQL 运算符优先级：AND 高于 OR

## 是什么

SQL 里 AND 优先级高于 OR（和 JS/Python 的 `&&`/`||` 一样）。想表达「匹配多组条件里的任意一组」，每组用括号包成完整布尔单元。

```sql
select * from orders
where (user_id = 12 and total = 10.00)
   or (user_id = 13 and total = 20.00)
   or (user_id = 14 and total = 30.00)
```

## 怎么用

- 跨行多组条件：`(a and b) or (c and d) or (e and f)`
- 同行的多个属性约束：`a = 1 and b = 2`（and 连接同一行的条件）
- 简单集合判断：`where user_id in (12, 13, 14)` 比 OR 串更清晰

## 关键点

- where 是对**每一行**做布尔判断，一次判断一行，不是声明期望的结果集
- 逗号不是 SQL 条件连接符，别用 Python 逗号分隔参数的直觉拼条件
- 匹配多行 = 让每行独立满足「组1 或 组2 或 组3」

## 陷阱

- 裸写不加括号、尾加新条件，因 AND 优先结合，新条件只约束最后一组——经典静默 bug
- where 只放行 true；NULL 参与比较是 unknown，永远被过滤（见 [[NULL三值逻辑]]）

## 相关笔记

[[SELECT查询骨架]] · [[NULL三值逻辑]]
