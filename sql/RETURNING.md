---
tags:
  - PostgreSQL
  - SQL
created: "2026-09-03"
---

# RETURNING（插入后拿回数据）

## 是什么

INSERT/UPDATE/DELETE 语句末尾加 `returning 列名`，操作完成后直接把**受影响行的真实列值**返回给你，省一趟验证查询。PG 特性，MySQL 没有。

```sql
insert into orders (user_id, total) values (15, 45.50) returning id;
-- 返回刚生成的那行 id
```

## 怎么用

```sql
-- 多列 / 全列
insert into orders (user_id, total) values (15, 45.50) returning id, total, created_at;
insert into orders (user_id, total) values (15, 45.50) returning *;

-- 批量插 N 行，returning 一次返回 N 行
update products set stock = stock - 2 where id = 3 returning id, stock;  -- 扣减后即时拿回
delete from orders where id = 33 returning id;                            -- 删除前快照
```

## 关键点

- 核心用途：拿回**你不知道的值**——自动生成的 id、默认值列。只回显你写过的值就没意义了
- 真实场景：创建订单 → 拿新 id → 写 order_items 子表 → 返回给前端，零额外查询
- 类比前端：创建接口直接返回新资源本体，省一次再 fetch

## 陷阱

- returning 返回的是数据库生成后的真实值，不是回显你 insert 写的值（id=37 不是 user_id=12）
- timestamptz 列返回的值显示随时区，库里存 UTC（见 [[timestamptz时间戳]]）

## 相关笔记

[[INSERT写操作]] · [[UPDATE与DELETE安全]] · [[SERIAL与IDENTITY]]
