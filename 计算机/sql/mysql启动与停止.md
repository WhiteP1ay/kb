---
tags:
  - mysql
  - 数据库
created: 2026-07-23
---

# mysql启动与停止

## 服务端相关：
- mysqld 这个可执行文件就代表着 MySQL 服务器程序。
- mysqld_safe 是一个启动脚本，它会间接的调用 mysqld ，还顺便启动了另外一个监控进程，帮助重启，以及记录错误日志。
- mysql.server 是一个启动脚本，它间接调用mysqld_safe。
## 客户端相关：
可以通过客户端程序来连接服务器程序，常用的是mysql。
- mysql -h主机名 -u用户名 -p密码
- mysql -hlocahhost -uroot -p 可以避免明文密码
- 和服务端在同一机器上可以省去输入-h

## 怎么用

通常用 mysql.server start 启动
用 mysql.server stop 停止

## 相关笔记
[[mysql客户端与服务端]] 
