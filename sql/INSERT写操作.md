---
tags:
  - SQL
created: "2026-09-03"
---

# INSERT 写操作

## 是什么

往表里插行。唯一模板是**显式列清单**：表名后括号列出要写的列，values 与之一一对应。没写的列交给数据库默认值。

```sql
insert into orders (user_id, total) values (11, 128.00);
```

## 怎么用

```sql
-- 批量：一个 values 挂多个括号，一条语句插 N 行
insert into orders (user_id, total) values (12, 10.00), (13, 20.00), (14, 30.00);
```

## 关键点

- 显式列清单 = 具名传参 `{userId, total}`；省略全列 = 把所有键按顺序裸传，表结构一加列就错位
- 默认值列不写：serial id 自动生成（nextval）、`created_at` 默认 `now()`
- **id 永远不要手填**：手填会让序列 nextval 落后，下一条自动 id 撞主键
- 字符串/日期用单引号，数字裸写

## 陷阱

- 省略列清单的 `insert into t values (1, 2, 3)` 是位置参数地狱，生产代码禁止
- 外键列值必须存在，否则 23503（见 [[外键与ON-DELETE]]）
- GUI/psql 默认自动提交；psycopg 代码里必须 commit 才落库（见 [[psycopg事务与commit]]）

## 相关笔记

[[RETURNING]] · [[SERIAL与IDENTITY]] · [[SELECT查询骨架]]
