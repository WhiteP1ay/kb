---
tags:
  - SQL
created: "2026-09-04"
---

# INNER JOIN：按外键配对

## 是什么

INNER JOIN 不是「A 为主去 B 里查」，是**配对操作**：A 每行 × B 每行两两组合，on 条件筛出配得上的行。两边地位平等，谁在 FROM、谁在 JOIN 不影响配对结果。

```sql
select o.id, o.total, u.name
from orders o
join users u on u.id = o.user_id;
```

## 怎么用

```sql
-- 8 月订单报表：订单 + 用户姓名 + 城市，金额降序
select o.total, u.name, u.city
from orders o
join users u on u.id = o.user_id
where o.created_at >= '2026-08-01' and o.created_at < '2026-09-01'
order by total desc;
```

## 关键点

- **对称性**：`from orders o join users u` 与 `from users u join orders o` 结果配对集合一样，只是行序可能不同
- **JOIN 拼的是键，不是名字**：users 里 id=1（北京）和 id=3（上海）都叫 A，join 结果 5 行都显示 A，但按 id 区分是两个不同的人——这就是为什么主键是 id，name 从不设唯一约束
- 一对多展开：users 8 行，join 后用户行被复制到他每笔订单上（A 下 5 单 → 结果里 A 出现 5 行）
- 要区分同名用户：把 u.id 或 u.city 也 select 出来

## 陷阱

- 拿 name 当身份对账 → 重名直接算错账
- 期望一行却出来多行 → 先想「一」侧是不是有多个「多」侧行

## 相关笔记

[[SQL联表查询]] · [[LEFT-JOIN反连接]] · [[SELECT查询骨架]] · [[INSERT写操作]]
