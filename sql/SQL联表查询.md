---
tags:
  - SQL
created: "2026-09-04"
---

# SQL 联表查询（JOIN）

## 是什么

两条 SELECT 之间没有变量通道——不能把第一条查到的 id「存进变量」给第二条用。要查 A 需要 B 的值，JOIN 把两次查找合成一条，由数据库内部完成衔接。外键（orders.user_id → users.id）是引用，JOIN 是按引用展开（前端 .find 的数据库版）。

## JOIN 家族语义

- **INNER**：两边匹配上的行才留（交集）。对称，FROM/JOIN 顺序无所谓
- **LEFT**：左表全保留，右表没匹配的补 NULL
- **RIGHT**：LEFT 反过来，右表全保留。实战几乎不用，想保留右边换边即可
- **FULL**：两边都全保留，各自补 NULL。罕见

实战 95% 是 INNER + LEFT。

## 怎么用

```sql
select o.id, u.name
from orders o
join users u on u.id = o.user_id   -- inner join，on 是拼接规则
where o.id = 19;
```

## 关键点

- 唯一模板：`from a join b on a.x = b.y`
- **左表不是「主表」，是「全保留方」**——只有 LEFT/RIGHT 才有方向，INNER 没有方向
- 选型：INNER 不用想顺序；LEFT 想清楚谁不能丢，谁不能丢谁在左
- 列名冲突起别名：`o.id as order_id`（as 只改这条查询的显示名，不改表）
- 手写两步查询再拼 = N+1 问题（N 条订单发 N+1 次查询），JOIN 一次到位

## 陷阱

- 忘了 on 或 on 写错 → 笛卡尔积，行数爆炸
- 以为 from 后面的表是「老大」——INNER 里没有老大
- join 结果行数变多是正常的：一对多时「一」侧的行被复制到每个「多」上

## 相关笔记

[[INNER-JOIN]] · [[LEFT-JOIN反连接]] · [[SELECT查询骨架]] · [[NULL三值逻辑]] · [[外键与ON-DELETE]]
