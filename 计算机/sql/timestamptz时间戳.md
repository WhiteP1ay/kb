---
tags:
  - PostgreSQL
  - SQL
created: "2026-08-31"
---

# timestamptz 时间戳

## 是什么

`TIMESTAMP WITH TIME ZONE`，PostgreSQL 的时间戳类型。内部统一存 UTC，查询显示时按会话时区自动转换。对应裸 `timestamp`（without time zone）是「没有时区概念的本地时间」，生产环境永远用 timestamptz。

## 怎么用

```sql
CREATE TABLE orders (
    created_at TIMESTAMPTZ DEFAULT now()
);
```

## 关键点

- 前端对应 `new Date().toISOString()` 的 `Z`（UTC 标记）——库里的时间是同一个时刻，显示各自转本地
- 用裸 timestamp 存「本地时间」，服务器换时区全库时间集体错乱，经典生产事故
- `DEFAULT now()`：插入时不写该列自动填当前时间（UTC 时刻）

## 陷阱

- 别用字符串拼时间，别用本地时间字符串入库，交给 timestamptz + now()
- 显示时依赖客户端/会话时区，不是库里改
- 2026-09-03 实测：同一条数据 DBeaver 显示 `15:49:21 +0800`（本地时区会话）、psql UTC 会话显示 `07:49:21+00`——库里只有一个 UTC 值，钟面数字随时区变。把本地钟面时间说成「UTC」是概念错误

## 相关笔记

[[SQL约束全景]]
[[外键与ON-DELETE]]
