---
tags:
  - SQL
created: "2026-09-03"
---

# UPDATE 与 DELETE 安全

## 是什么

改行与删行。唯一模板都带 `where` 精确定位：

```sql
update orders set total = 99.00 where id = 37;
delete from orders where id = 37;
```

## 怎么用

```sql
-- set 多列逗号分隔
update products set price = 6.50, stock = 10 where id = 3;

-- 原子更新：基于当前值减，不是拍死绝对值（扣库存/扣余额专用）
update products set stock = stock - 2 where id = 3 returning stock;

-- 批量删除/更新：where 里用 in
delete from orders where user_id in (12, 13, 14);
```

## 关键点

- **where 是保命符不是可选项**：忘写 = 作用于全表每一行。UPDATE 忘写全员金额清零，DELETE 忘写整表清空，autocommit 下无撤销
- 跑完看返回的 `UPDATE N` / `DELETE N`——N 是实际影响行数，改 1 行该是 1，是 8 就闯祸了
- `set stock = stock - 2` 是数据库原子的（行锁），并发不丢更新；先 select 再算再 update 会撞并发丢更新。类比前端 reducer 基于 prev state
- 前端改内存对象天然只动目标；SQL 没有「默认只改一条」的保护——这是最大的思维分叉

## 陷阱

- DELETE 前先数 where 条件个数，确认精确指向目标行
- 删被外键引用的行会触发 ON DELETE 策略：RESTRICT 拦截、CASCADE 连带删子表行（见 [[外键与ON-DELETE]]）

## 相关笔记

[[INSERT写操作]] · [[外键与ON-DELETE]] · [[RETURNING]]
