---
tags:
  - PostgreSQL
  - SQL
created: "2026-08-31"
---

# 外键与 ON DELETE

## 是什么

外键（FOREIGN KEY）表达表间从属关系。父 = 被引用的表（REFERENCES 指向的目标），子 = 外键所在表。子必须先有父才能存在，父的生死受子约束。

## 怎么用

```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
    total NUMERIC(10, 2) NOT NULL
);

CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INTEGER NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
    ...
);
```

## 关键点

- 父子判断：外键写在谁身上谁就是子，REFERENCES 指向谁谁就是父
- 一对多：一个父对应多个子（一个用户多个订单）
- 删父行三策略：`RESTRICT`（默认，有引用就禁止删）、`ON DELETE CASCADE`（删父连带删子）、`ON DELETE SET NULL`（删父后子行外键变 NULL，列需可空）
- 选型：历史/财务数据用 RESTRICT（订单不能丢），从属数据用 CASCADE（明细没订单没意义），弱关联用 SET NULL
- 改策略：`ALTER TABLE ... DROP CONSTRAINT` + `ADD CONSTRAINT`

## 陷阱

- 插子行引用不存在的父：`23503`，Key is not present in table
- 删父行被引用：`23503`，still referenced from table——先删子或改策略
- 自增 id 随删除不回退，别用「id 连续」当业务假设

## 相关笔记

[[SQL约束全景]]
[[SERIAL与IDENTITY]]
[[NUMERIC精确小数]]
