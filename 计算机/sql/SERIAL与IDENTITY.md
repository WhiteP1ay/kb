---
tags:
  - PostgreSQL
created: "2026-08-31"
---

# SERIAL 与 IDENTITY（自增主键）

## 是什么

自增主键的三种写法，按方言区分：

- MySQL：`AUTO_INCREMENT`
- PostgreSQL 旧式：`SERIAL`（PG 方言）
- SQL 标准 / PG 新写法：`GENERATED ALWAYS AS IDENTITY`

## 怎么用

```sql
-- PG 旧式（教程常见）
CREATE TABLE products (id SERIAL PRIMARY KEY, ...);

-- PG 新式（SQL 标准，推荐）
CREATE TABLE products (id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY, ...);

-- MySQL
CREATE TABLE products (id INT AUTO_INCREMENT PRIMARY KEY, ...);
```

## 关键点

- `SERIAL` 是 PG 语法糖：背后是 `INTEGER NOT NULL` + 自动创建 sequence + 默认值 `nextval('xxx_id_seq')`
- PG 10+ 推荐 IDENTITY（标准、权限更好控制），存量代码和教程仍大量用 SERIAL，两种都要认识
- 自增列插数据时不写该列，数据库自动分配

## 陷阱

- 自增值不随删除回退：删了 id=3，下一个还是 4，别指望它连续
- 重跑建表脚本会 `DuplicateTable`（见 [[psycopg事务与commit]] 的幂等性教训）

## 相关笔记

[[psycopg事务与commit]]
[[PostgreSQL角色模型]]
[[mysql启动与停止]]
