---
tags:
  - PostgreSQL
  - SQL
created: "2026-08-31"
---

# NUMERIC 精确小数

## 是什么

数据库的定点十进制类型，按十进制整数精确存储，计算永远精确。SQL 标准类型，PostgreSQL 叫 `NUMERIC(p, s)`，MySQL 叫 `DECIMAL(p, s)`，两者等价。前端类比：Python 的 `Decimal`、Java 的 `BigDecimal`。

## 怎么用

```sql
price NUMERIC(10, 2)   -- 总长 10 位，小数 2 位：最大 99999999.99
```

## 关键点

- 浮点（float/double）是二进制近似，`0.1 + 0.2` 不等于 0.3（前端早就见过）
- **钱、数量、汇率必须用 NUMERIC/DECIMAL，永远别用 float**——支付、订单、财务报表用 float 存钱，对账对不平是分分钟的事
- NUMERIC 不是 PG 方言，SQL 标准，MySQL 的 DECIMAL 是同一个东西

## 陷阱

- 浮点转 NUMERIC 有精度损失风险，先算好再存
- `NUMERIC(10, 2)` 存 `123456789.99` 会溢出报错（总长超 10 位），设计时留足长度

## 相关笔记

[[SERIAL与IDENTITY]]
[[psycopg事务与commit]]
