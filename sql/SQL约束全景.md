---
tags:
  - PostgreSQL
  - SQL
created: "2026-08-31"
---

# SQL 约束全景（PostgreSQL）

## 是什么

数据库层面的数据规则，写死在表结构里，任何客户端、任何代码路径都躲不过。应用层校验（Zod）是礼貌，数据库约束是法律。

## 怎么用

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email TEXT UNIQUE NOT NULL,
    age INTEGER CHECK (age >= 0 AND age <= 150)
);
```

## 关键点

- 五大约束：`PRIMARY KEY`（唯一+非空，一表一个）、`UNIQUE`（值不重复，可有多个）、`NOT NULL`（拒绝 NULL）、`CHECK`（布尔条件，为假拒绝）、`FOREIGN KEY`（引用别的表）
- 主键 = UNIQUE + NOT NULL 的合体
- 错误码都是 23xxx（完整性约束违反）：`23505` unique violation、`23514` check violation、`23503` foreign key violation

## 陷阱

- 约束是兜底，不是替代应用层校验：前端该校验还校验（体验），数据库约束管真相（可靠性）
- 重跑 CREATE TABLE 报 `DuplicateTable`（42P07），脚本要幂等：`CREATE TABLE IF NOT EXISTS` 或迁移工具

## 相关笔记

[[外键与ON-DELETE]]
[[SERIAL与IDENTITY]]
[[psycopg事务与commit]]
