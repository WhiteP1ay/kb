---
tags:
  - SQL
created: "2026-09-17"
---

# GROUP BY 分组聚合

## 是什么

把多行压成少行的操作。按你给的键把行分堆，键相同的凑一堆，每堆算一个值，一堆塌成一行。orders 19 行按用户分组，结果 7 行。

拿 Excel 打比方，先按用户列排序，同一个人的订单凑成连续几段，每段算个合计，段数就是结果行数。

## 怎么用

```sql
select 分组键, 聚合函数(某一列)
from 表
group by 分组键;

select u.id, u.name, count(*) as order_cnt, sum(o.total) as spent
from users u
join orders o on o.user_id = u.id
group by u.id, u.name
order by spent desc;
```

## 关键点

- **铁律：select 里只能出现两样东西**，分组键本身，或者套了聚合函数的列。别的列一个都不行
- 违反铁律时 PG 直接拦：`column "o.total" must appear in the GROUP BY clause or be used in an aggregate function`。原因是那一列在一堆里有多个值，数据库不知道该还你哪个
- **分组键要能唯一代表一组**。库里 id 1 和 id 3 两个用户都叫 A，按 name 分组会把他俩算成一组（165.20 + 125.50 = 290.70）。带上 id：`group by u.id, u.name`
- 结果只包含出现过的键值。没卖过的商品在 order_items 里没有行，分不出那一堆，所以不会出现，要它出现得从 products 出发做左连接（见 [[LEFT-JOIN反连接]]）

## 陷阱

- 分组键漏带能区分身份的列 → 同名数据被静默合并
- 以为 group by 会保留原始行数，实际它一定减少行数（除非每组恰好一行）
- 把过滤直接写在 where 里筛聚合值 → 报错，得用 [[HAVING与WHERE]]

## 相关笔记

[[聚合函数]] · [[HAVING与WHERE]] · [[SELECT查询骨架]] · [[SQL联表查询]] · [[LEFT-JOIN反连接]]
