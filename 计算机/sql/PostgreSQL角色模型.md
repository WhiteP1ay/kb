---
tags:
  - PostgreSQL
created: "2026-08-31"
---

# PostgreSQL 角色模型

## 是什么

PostgreSQL 没有 root。权限模型是**角色（role）**，比 MySQL 的「用户」更底层：角色可以带 `LOGIN` 属性（能登录），也可以不带（只当组用，归集权限）。安装时 PG 自动创建一个超级角色，名字 = 系统用户名（macOS）或 `postgres`（Docker），本机连接免密（peer 认证）。

## 怎么用

```sql
CREATE ROLE shop_user WITH LOGIN PASSWORD 'shop123';   -- 建专属角色
CREATE DATABASE shop_db OWNER shop_user;               -- 建库并授权
```

```bash
psql -d postgres -c "SQL"          # 用默认超级角色执行
docker exec pg-shop psql -U postgres -c "SQL"   # Docker 里是 postgres
```

## 关键点

- 工程铁律：别用超级角色跑业务，每个应用建专属角色 + 专属库，权限最小化
- Docker 镜像超级用户是 `postgres`，密码由环境变量 `POSTGRES_PASSWORD` 指定
- `CREATE ROLE` 默认不带 LOGIN，必须显式 `WITH LOGIN`

## 陷阱

- MySQL 迁移者找 root 找不到：PG 根本没有，超级角色名 = 系统用户名或 postgres
- 命令行坑：psql 的 `-p` 是端口（MySQL 的 `-p` 是密码）、`-U` 大写是用户、`-d` 是库名
- 建库链式依赖：`OWNER shop_user` 引用前面的角色，角色没建成功库也建不成

## 相关笔记

[[SERIAL与IDENTITY]]
[[psycopg事务与commit]]
[[mysql客户端与服务端]]
