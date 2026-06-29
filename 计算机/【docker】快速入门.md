# Docker 入门：从"在我机器上能跑"到一键部署

"在我电脑上能跑啊！"——这大概是程序员最常说的话之一。Node 版本不一样、系统库缺失、环境变量没配……这些"环境差异"是部署的头号噩梦。

Docker 解决的就是这个问题：**把应用和它的运行环境一起打包，到哪都能原样跑起来**。

---

## 一、Docker 是什么？

Docker 是一个**容器化平台**。它把应用及其所有依赖（代码、运行时、系统工具、系统库）打包到一个独立的、可移植的容器中。

三个核心概念：

- **镜像**（Image）：一个只读模板，包含运行应用所需的一切。类比：操作系统的 ISO 文件、Java 的 Class。
- **容器**（Container）：镜像的运行实例。类比：用 ISO 安装好的系统、JVM 中的对象。一个镜像可以启动多个容器。
- **仓库**（Repository）：存放镜像的地方。[Docker Hub](https://hub.docker.com) 是最主要的公共仓库，类似 GitHub 之于代码。

---

## 二、为什么不是虚拟机？

很多人第一反应：这不就是轻量级虚拟机吗？其实差别很大。

| 对比维度 | 虚拟机 | Docker 容器 |
|---------|--------|------------|
| 虚拟化层级 | 硬件层 | 操作系统层 |
| 操作系统 | 每个 VM 有完整的 Guest OS | 共享宿主机内核 |
| 启动速度 | 分钟级 | 秒级 |
| 资源占用 | 数 GB 内存起 | 几十 MB 起 |
| 隔离性 | 强（完全隔离） | 进程级隔离 |

虚拟机的架构：

```
应用 → 依赖库 → Guest OS → Hypervisor → Host OS → 硬件
```

Docker 容器的架构：

```
应用 → 依赖库 → Docker Engine → Host OS → 硬件
```

关键区别：**Docker 容器没有自己的操作系统内核**，它直接共用宿主机的内核。这让容器变得极轻——一个 Ubuntu 基础镜像才几十 MB，而一个 Ubuntu 虚拟机至少几个 GB。

这也是 Docker 能在单台物理机上跑成百上千个容器的原因，对服务器性能压榨到极致。

> 注意：这也意味着 Linux 容器只能在 Linux 宿主机上运行。macOS/Windows 上的 Docker 实际上在后台跑了一个轻量 Linux VM。

---

## 三、Docker 架构

Docker 采用 **Client-Server** 架构：

```
docker CLI（客户端）
     ↓ REST API（Unix Socket / TCP）
Docker Daemon（守护进程）
     ↓
容器管理（创建、启动、停止、网络、存储……）
```

日常使用的 `docker run`、`docker build` 等命令，实际上都是通过 REST API 发给 dockerd 守护进程执行的。

---

## 四、基础命令速查

### 镜像操作

```bash
docker images                          # 查看本地所有镜像
docker images -a                       # 包括中间层
docker images -q                       # 只显示镜像 ID

docker pull mysql:8.0                  # 从仓库拉取镜像（不写 tag 默认 latest）
docker pull nginx                      # 等价于 docker pull nginx:latest

docker rmi <镜像名或ID>                # 删除镜像
docker rmi -f $(docker images -aq)     # 强制删除所有镜像
```

> 拉取镜像是**分层下载**的。不同镜像可以共享相同的底层 Layer（比如都用 `debian:bookworm-slim` 做基础镜像），极大减少磁盘占用和下载时间。

### 容器操作

```bash
# 创建并启动容器
docker run -d --name myapp -p 8080:80 nginx

# 参数说明：
# -d              后台运行
# --name          给容器命名（方便后续操作）
# -p 8080:80      将宿主机 8080 端口映射到容器 80 端口
# -e KEY=VALUE    设置环境变量
# -it             交互模式 + 分配伪终端（进入容器调试时用）
# --rm            容器退出时自动删除（常用于测试）
# -v 宿主机:容器   挂载数据卷（后面详解）

# 查看容器
docker ps                              # 正在运行的
docker ps -a                           # 所有（包括已停止的）
docker ps -aq                          # 只显示所有容器 ID

# 启停容器
docker start <容器名>                   # 启动已停止的容器
docker stop <容器名>                    # 优雅停止（SIGTERM → 等待 → SIGKILL）
docker restart <容器名>                 # 重启
docker kill <容器名>                    # 强制停止（SIGKILL）

# 删除容器
docker rm <容器名>                      # 删除已停止的容器
docker rm -f $(docker ps -aq)          # 强制删除所有容器

# 进入容器
docker exec -it <容器名> /bin/bash      # 最常用：开启新终端进入容器
docker attach <容器名>                  # 连接到容器的主进程（很少用）
```

### 查看信息

```bash
docker logs <容器名>                    # 查看容器日志
docker logs -f <容器名>                 # 持续跟踪（follow）
docker logs -ft --tail 50 <容器名>     # 带时间戳，显示最近 50 行

docker top <容器名>                     # 查看容器内运行的进程
docker stats                           # 实时查看所有容器的资源占用（CPU/内存/网络）

docker inspect <容器名>                 # 查看容器完整元数据（JSON 格式）
# 输出包含：容器 ID、创建时间、状态、网络配置、挂载信息……
```

> 常见坑：`docker run -d centos` 启动后容器立刻退出。因为 CentOS 镜像没有前台进程在跑——Docker 检测到没有应用在运行就自动停止了。解决方案：给一个持续运行的前台命令，比如 `docker run -d centos tail -f /dev/null`。

---

## 五、分层镜像与 Commit

### 联合文件系统（UnionFS）

Docker 镜像由一层层只读的**文件系统 Layer** 堆叠而成，这叫联合文件系统。基础层通常是操作系统镜像（如 Debian），往上的每一层是一次修改（安装软件、复制文件等）。

当你 `docker run` 时，Docker 在最顶上加了一层**可写层**（容器层）。你对容器的所有修改都写在这一层。底层镜像的所有 Layer 都是只读的，多个容器共享。

这种分层设计让镜像复用变得高效——10 个基于 `node:22` 的应用镜像，共享同一个 Node.js 底层，只在上层有自己的代码。

### docker commit

像虚拟机的快照一样，你可以把对容器的修改保存为一个新镜像：

```bash
docker commit -a="作者名" -m="描述" <容器名或ID> <新镜像名>:<tag>
```

示例：你启动一个 Tomcat 容器，发现官方镜像缺少 webapps 目录，手动补上了。`docker commit` 可以把修复后的容器保存为新镜像，以后直接用。

不过实际开发中，更推荐用 **Dockerfile** 来定义镜像——可重复、可版本控制、可审查。

---

## 六、数据卷：让数据活过容器的生命周期

Docker 容器被删除后，容器内的数据也随之消失。但数据库文件、用户上传的图片这些数据显然不该随容器一起死。

**数据卷**（Volume）就是解决方案——把容器内的目录映射到宿主机。

### 基本用法

```bash
docker run -v 宿主机目录:容器目录 <镜像>
```

挂载后，两边实时同步。你在宿主机改文件，容器内立刻看到；容器生成的日志写进挂载目录，容器删了，日志还在。

检查是否挂载成功：

```bash
docker inspect <容器名> | grep -A 10 Mounts
```

### 实战：MySQL 数据持久化

```bash
docker run \
  --name my-mysql \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=secure123 \
  -v /data/mysql/conf:/etc/mysql/conf.d \
  -v /data/mysql/data:/var/lib/mysql \
  -d mysql:8.0
```

即使删掉容器，`/data/mysql/data` 里的数据库文件依然在。下次启动新容器时挂载同一个目录，数据毫发无损。

### 具名挂载 vs 匿名挂载

```bash
# 具名挂载（推荐）：给卷起个名字
docker run -v myvolume:/app/data nginx
# 卷会存放在 Docker 管理的区域，用名字引用

# 匿名挂载：不指定宿主机路径，Docker 自动分配
docker run -v /app/data nginx
# 也可以通过 Dockerfile 的 VOLUME 指令创建
```

卷管理命令：

```bash
docker volume ls                       # 列出所有卷
docker volume inspect <卷名>            # 查看卷的详情（实际存储位置等）
```

### 容器间共享卷：--volumes-from

```bash
# 容器 A 挂载了数据卷
docker run --name data-provider -v /data busybox

# 容器 B 继承容器 A 的所有卷
docker run --name data-user --volumes-from data-provider ubuntu
```

注意：
- 源容器不需要运行，只要存在就行
- 可以从多个容器继承
- 删除源容器**不会**影响已继承的卷（卷是独立的）
- 继承的是挂载点，不是数据副本

### 读写权限

```bash
docker run -v /host/path:/container/path:ro nginx   # 只读
docker run -v /host/path:/container/path:rw nginx   # 可读写（默认）
```

---

## 七、Dockerfile：用代码定义镜像

`docker commit` 虽然方便，但你无法知道镜像到底经过了哪些修改。**Dockerfile** 用声明式的方式定义镜像的构建过程——可重复、可审查、可放进 Git。

### 构建流程

```
编写 Dockerfile → docker build → 镜像 → docker run → 容器 → docker push → 发布到仓库
```

### 核心指令

```dockerfile
# 指定基础镜像（必须有，且一般是第一行）
FROM node:22-alpine

# 维护者信息（可选）
LABEL maintainer="yourname@example.com"

# 设置工作目录（后续 RUN/CMD/COPY 都相对于此目录）
WORKDIR /app

# 复制文件到镜像
COPY package.json pnpm-lock.yaml ./

# 构建时执行命令
RUN pnpm install

# 复制源码
COPY . .

# 声明容器运行时监听的端口（文档作用，不实际映射端口）
EXPOSE 3000

# 设置环境变量
ENV NODE_ENV=production

# 容器启动时执行的命令（只有最后一个生效）
CMD ["node", "server.js"]

# 和 CMD 类似，但不可被 docker run 后的参数覆盖
# ENTRYPOINT 更适用于把容器当"可执行文件"用
ENTRYPOINT ["node", "server.js"]
```

### CMD vs ENTRYPOINT

```dockerfile
# CMD：可以被 docker run 后面的命令覆盖
CMD ["ls", "-a"]
# docker run myimg ls -l  →  实际执行 ls -l（CMD 被覆盖了）

# ENTRYPOINT：docker run 后的参数会追加到 ENTRYPOINT 后面
ENTRYPOINT ["ls", "-a"]
# docker run myimg -l  →  实际执行 ls -a -l（参数被追加）
```

### 查看构建历史

```bash
docker history <镜像名>
# 显示每一层的创建方式、大小
```

---

## 八、Docker 网络

### 网络模式

```bash
docker network ls
```

四种模式：

- **bridge**（默认）：容器通过虚拟网桥与宿主机通信。最常用。
- **host**：容器直接使用宿主机网络栈，性能最好但隔离性最差。
- **none**：容器没有网络接口。
- **container**：容器共享另一个容器的网络命名空间。

### 自定义网络

推荐为每个项目创建独立的网络，让容器通过容器名互相访问：

```bash
# 创建自定义网络
docker network create \
  --driver bridge \
  --subnet 192.168.0.0/16 \
  --gateway 192.168.0.1 \
  mynet

# 启动容器时指定网络
docker run -d --network mynet --name app1 myapp
docker run -d --network mynet --name app2 myapp

# 现在 app1 可以直接 ping app2（按容器名解析）
```

好处：不同项目使用不同的网络，容器之间天然隔离。一个项目挂了不会影响另一个。

### 跨网络联通

```bash
# 让一个容器加入另一个网络
docker network connect mynet container-name
```

联通后，这个容器就有了**两个 IP 地址**（每个网络各一个），可以同时访问两个网络中的容器。

---

## 总结

Docker 解决了软件开发中最头疼的"环境一致性"问题，学习曲线核心在三个点：

1. **理解镜像和容器的关系**：镜像是模板，容器是实例
2. **掌握数据卷**：数据不该死在容器里，用 `-v` 挂出来
3. **写 Dockerfile**：用代码定义环境，告别"在我机器上能跑"

当你装一个 MySQL 只需要一行 `docker run`、切换 Node 版本不需要 nvm、CI/CD 构建环境完全一致的时候，你就会觉得 Docker 真香了。

---

*本文整理自个人学习笔记，力求用通俗语言讲清核心概念。如有疏漏，欢迎指正。*
