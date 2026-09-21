---
tags:
  - PostgreSQL
  - SQL
created: "2026-08-31"
---

# SQL 引号与大小写规则

## 是什么

SQL 的引号和大小写规则和 JS/Python 完全不同，从这两门语言迁移过来必踩坑。核心：单引号管值、双引号管名字；标识符不引号折叠小写。

## 怎么用

```sql
-- 单引号 = 字符串值
INSERT INTO users (email) VALUES ('a@b.com');

-- 双引号 = 标识符（列名/表名）——值的位置写双引号会报 column does not exist
-- "a@b.com" 会被当成名叫 a@b.com 的列
```

## 关键点

- 单引号 `'...'` = 字符串值；双引号 `"..."` = 标识符（列名/表名）
- 关键字大小写不敏感（惯例大写，全小写也合法）
- **未加引号的标识符全部折叠成小写**：`Users`、`USERS` 都是 `users`
- 加引号的标识符区分大小写：`"Users"` ≠ `users`，自找麻烦，别用
- 字符串值大小写敏感：`'a@b.com'` ≠ `'A@B.COM'`，忽略大小写用 ILIKE 或 lower()

## 陷阱

- MySQL 默认双引号也能当字符串（ANSI_QUOTES 关），惯坏了迁移者，PostgreSQL 严格区分
- 最佳实践：值永远单引号，表名列名小写不加引号

## 相关笔记

[[SQL约束全景]]
[[外键与ON-DELETE]]
[[psycopg事务与commit]]
