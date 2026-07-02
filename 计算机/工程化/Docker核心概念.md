---
tags:
  - 工程化
  - Docker
created: "2026-07-02"
---

# Docker 核心概念

## 是什么

Docker 把应用和环境打包成容器，解决「在我电脑上能跑」问题。镜像（模板）→容器（实例）→仓库（存放镜像的地方，如 Docker Hub）。

## 关键点

- vs 虚拟机：Docker 共享宿主机内核，无 Guest OS，秒级启动，几十 MB 起
- 分层镜像：基础层+逐层叠加，多个镜像共享底层，节省磁盘
- 数据卷 -v：容器删除数据不丢，挂载宿主机目录
- Dockerfile 核心指令：FROM、WORKDIR、COPY、RUN、CMD/ENTRYPOINT、EXPOSE
- docker run -d --name app -p 8080:80 image
