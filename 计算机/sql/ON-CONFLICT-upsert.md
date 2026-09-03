---
tags:
  - PostgreSQL
  - SQL
created: "2026-09-03"
---

# ON CONFLICT（upsert）

## 是什么

「有就更新，没有就插入」一条语句干完，省掉先查再决定的往返。PG 的 `insert ... on conflict`；MySQL 对应 `insert ... on duplicate key update`。

```sql
insert into products (name, price) values ('tomato', 6.00)
on conflict (name) do update set price = excluded.price;
```

## 怎么用

```sql
-- 撞了更新（旧行价格改成新数据带进来的价格）
insert into products (name, price) values ('tomato', 6.00)
on conflict (name) do update set price = excluded.price;

-- 撞了安静跳过
insert into products (name, price) values ('tomato', 6.00)
on conflict (name) do nothing;
```

## 关键点

- 前提：`on conflict (列)` 的列**必须是唯一约束或主键**，否则直接报错
- `excluded` 引用「本次想插入的那行」；裸写列名（不带前缀）指表里**已存在的旧行**
- `set price = excluded.price` 翻译：把旧行价格改成我这次想带进来的价格
- 适用场景：价格同步、批量导入、注册去重

## 陷阱

- 没给冲突列加唯一约束就跑 upsert → 报错 not matching ON CONFLICT
- 唯一约束命名规律：`表名_列名_key`，删约束 `alter table products drop constraint products_name_key;`
- 撞唯一约束的报错信息带约束名，能直接定位哪张表哪列

## 相关笔记

[[INSERT写操作]] · [[SQL约束全景]] · [[UPDATE与DELETE安全]]
