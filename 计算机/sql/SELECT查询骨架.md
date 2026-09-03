---
tags:
  - SQL
created: "2026-09-01"
---

# SELECT 查询骨架

## 是什么

SQL 查询的固定子句顺序，对应 JS 的链式数组操作：

```sql
select 列名             -- 投影：要哪些列（* = 全部）
from 表名
where 条件             -- 过滤行：and/or 组合比较条件
order by 列 [asc|desc]  -- 排序：asc 升序（默认），desc 降序
limit 数字;            -- 截断：取前 N 行
```

## 怎么用

多列排序：`order by user_id asc, total desc` —— 先按第一列排，相同再按第二列；`desc` 只管它前面那一列。

分页公式：第 N 页 = `limit 页大小 offset (N-1) × 页大小`（前端 `page=2&size=5` 对应 `limit 5 offset 5`）。

去重：`select distinct 城市 from users`，作用于 select 出来的整行组合（类比 JS 的 `new Set()`）。

## 关键点

- 子句顺序是铁律：where → order by → limit，不能调换
- `and`/`or` 是 where 内部的逻辑运算符，不能用来连接子句
- 日期查月份用半开区间：`created_at >= '2026-08-01' and created_at < '2026-09-01'`，`>` 会漏掉月初零点
- `limit` 在排序之后截断：先 order by 排好，再切前 N 行

## 陷阱

- 不是 `sort by`！SQL 是 `order by 列名`，desc 不能裸奔（JS `.sort()` 的肌肉记忆）
- `between` 是闭区间，含两端：`rating between 1 and 5` 允许 1 和 5
- 少读一个 where 条件 = 交错答案：写完对照需求逐条核对

## 相关笔记

[[NULL三值逻辑]] · [[timestamptz时间戳]] · [[SQL约束全景]]
