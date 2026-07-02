---
tags:
  - SQL
  - 基础
created: "2026-07-02"
---

# SQL LEFT JOIN vs RIGHT JOIN

## 是什么

LEFT JOIN：左表为主表，保留所有左表记录，右表不匹配填 NULL。RIGHT JOIN 相反。主表是保留所有记录的表，不是「大多数列重合」的表。

## 关键点

- `SELECT * FROM a LEFT JOIN b ON a.id = b.id` → a 的所有行都在，b 没匹配的列填 NULL
- 选择 LEFT/RIGHT：看哪张表的行需要全部保留
- 常见坑：以为主表=列多的表，实际主表=要全保留的表
- 去重技巧：`WHERE col = (SELECT DISTINCT col ORDER BY ... LIMIT offset,1)`
