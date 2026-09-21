---
tags:
  - SQL
created: "2026-09-04"
---

# LEFT JOIN 与反连接

## 是什么

LEFT JOIN = 先做 INNER 配对，再把**左表没配上的行追加进来**，右边补 NULL。

业务场景：渲染用户列表，没下过单的用户也要出现（显示暂无订单），而不是整个人消失——INNER JOIN 做不到。

```sql
select u.id, u.name, o.id as order_id
from users u
left join orders o on o.user_id = u.id;
-- 8 用户：7 个有订单展开成 19 行 + 杨乐 1 行补 NULL = 20 行
```

## 怎么用

反连接（anti-join）：找「左边有、右边没有」的孤儿行——注册了从不消费的用户、从没被买过的商品。

```sql
select u.id, u.name
from users u
left join orders o on o.user_id = u.id
where o.id is null;   -- 只留没配上的行：杨乐
```

## 关键点

- 左表 = 全保留方，不是「主表」——LEFT JOIN **换边就变天**：users 在左杨乐出现，orders 在左杨乐消失（他没订单可当左行）
- 判 NULL 用 `is null`，不能用 `= null`（见 [[NULL三值逻辑]]）
- **NULL ≠ 0**：0 是有值、值为零；NULL 是没有值

## 陷阱

- 忘写 `where ... is null` → 退回普通 LEFT JOIN，多出所有匹配行
- 写成 `= null` → 语法合法但永远 0 行
- LEFT JOIN 后行数 = INNER 行数 + 左表没配上的行数

## 相关笔记

[[SQL联表查询]] · [[INNER-JOIN]] · [[NULL三值逻辑]] · [[外键与ON-DELETE]]
