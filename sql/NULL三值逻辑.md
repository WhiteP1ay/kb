---
tags:
  - SQL
created: "2026-09-01"
---

# NULL 三值逻辑

## 是什么

SQL 的逻辑是三值的：true / false / **unknown**。NULL 表示「未知/缺失」，不是 0，不是空字符串。

NULL 参与任何比较（`=` `!=` `>` `<` `>=` `<=`）结果都是 unknown，而 `where` 只放行 true——所以 NULL 行永远被过滤。

## 怎么用

```sql
select * from products where price is null;       -- 只要 NULL 行
select * from products where price is not null;   -- 排除 NULL 行
```

想「排除某个值但保留 NULL」：显式补回

```sql
select * from products where price != 50 or price is null;
```

## 关键点

- 口诀：**NULL 不能比，只能 is**
- `price = null` 语法合法但永远匹配不到任何行
- 三值逻辑下，`not (条件)` 不等于「条件之外的所有行」——NULL 行两边都不站
- JS 里 `undefined != 50` 是 true；SQL 里 NULL 参与比较是 unknown——行为不同，别用 JS 直觉

## 陷阱

- 业务上「非 50 的商品」要不要包含没定价的？取决于有没有显式 `or price is null`
- `distinct` 会把多个 NULL 合并成一行（NULL 们互相相等）

## 相关笔记

[[SELECT查询骨架]] · [[SQL约束全景]]
