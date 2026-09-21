---
tags:
  - PostgreSQL
  - Python
created: "2026-08-31"
---

# psycopg 事务与 commit

## 是什么

psycopg 3 默认 `autocommit=False`：所有 `execute` 都在一个隐式事务里，数据只存在于内存中的事务快照。只有显式 `conn.commit()` 才落盘，关闭连接时未提交的事务**全部回滚**。

## 怎么用

```python
conn.execute("CREATE TABLE products (...)")          # DDL 也在事务里
conn.execute("INSERT INTO products (name, price) VALUES (%s, %s)", ("onion", 3.50))
conn.commit()   # 落盘，一次提交所有变更
cur = conn.execute("SELECT * FROM products")
print(cur.fetchall())
conn.close()
```

## 关键点

- 与前端 state 的本质差异：setState 即时生效，数据库必须 commit 才生效，不提交等于没干
- 三通道提交行为对照（2026-09-03 db-4 实测）：DBeaver GUI autocommit ON（工具栏 Auto 亮起）每条语句立即落库；psql 同样默认自动提交；**psycopg 默认 False 必须显式 commit**——写代码时别拿 GUI 直觉套 psycopg
- 一次 commit 提交连接上所有未提交的变更（一个事务）
- 参数化 `%s` 是必须的，别用 f-string 拼 SQL（SQL 注入）

## 陷阱

- 实战踩坑：建表 + 插入后直接 `conn.close()` 不 commit，查库发现表根本不存在，全回滚
- 脚本不幂等：裸 `CREATE TABLE` 跑第二次报 `DuplicateTable`，用 `CREATE TABLE IF NOT EXISTS` 或迁移工具（Alembic）
- 查询结果行是元组：`fetchone()` 返回 `(1,)`，打印方法对象 `cur.fetchone`（无括号）输出 `<bound method ...>` 是忘了调用

## 相关笔记

[[SERIAL与IDENTITY]]
[[NUMERIC精确小数]]
[[类型标注与运行时]]
