---
tags:
  - SQL
created: "2026-09-17"
---

# HAVING 与 WHERE 的分工

## 是什么

两个过滤口，位置不同，管的东西不同。

- `where` 在分组**之前**筛行
- `having` 在分组**之后**筛组

执行顺序是 `from → where → group by → having → order by → limit`。跑 where 的时候行还是一行一行的，聚合值还不存在，所以拿聚合函数去 where 里筛，数据库直接禁止。

```sql
where sum(o.total) > 200
-- ERROR: aggregate functions are not allowed in WHERE
```

## 怎么用

```sql
select u.id, u.name, sum(o.total) as spent
from users u
join orders o on o.user_id = u.id
group by u.id, u.name
having sum(o.total) > 200
order by spent desc;
```

## 关键点

- 判断标准只有一条：**这个条件里的值在分组前就已经确定了吗**。已经确定的写 where，要等分组算完才知道的写 having
- 能塞进 `where` 的条件就别写 `having`。where 先动手，行数减少，后面分组的活就轻；having 是等分组全算完才筛，白算的活都算过了
- `having` 里除了聚合值，也可以写分组键

## 陷阱

- 同一个表达式在一句里出现多次（select、having、order by 各一次），改的时候漏一处 → 静默错结果。改之前数一遍它出现了几次
- 把 `where` 的条件挪进 `having` → 结果一样但白算，数据量一大就是性能问题

## 相关笔记

[[GROUP-BY分组聚合]] · [[聚合函数]] · [[SELECT查询骨架]]
