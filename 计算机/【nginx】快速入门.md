# Nginx 入门：从反向代理到负载均衡

Nginx 是每个后端开发者迟早要打交道的组件。它轻量、高性能、配置简洁，几乎成了现代 Web 架构的"标配网关"。这篇文章带你搞懂 Nginx 是什么、能干什么、以及怎么配置。

---

## 一、Nginx 是什么？

一句话：**一个高性能的 HTTP 和反向代理服务器**。

两个关键词：

- **高性能**：采用异步非阻塞的事件驱动模型，单机就能支撑数万并发连接。内存占用极低（几 MB）。
- **反向代理**：Nginx 最核心的使用场景——站在服务器端，替后端应用接收客户端请求。

> 和 Apache 的区别？Apache 是"一个请求一个进程/线程"的模型，并发一高就撑不住。Nginx 用事件驱动 + 少量 worker 进程处理所有请求，在面对高并发和静态文件时效率碾压 Apache。

---

## 二、正向代理 vs 反向代理

很多新手卡在这两个概念上，其实区分很简单——**看代理站在哪一边**。

### 正向代理（Forward Proxy）

代理的是**客户端**。你通过代理服务器访问外部网络，目标服务器不知道真实的客户端是谁。

```
你 → 代理服务器 → 目标网站
```

典型场景：科学上网、企业内网代理出口。

### 反向代理（Reverse Proxy）

代理的是**服务器端**。客户端请求打到 Nginx，Nginx 根据规则转发到后端的某台应用服务器。客户端完全不知道背后有多少台服务器、分别在哪。

```
客户端 → Nginx → 应用服务器1
              → 应用服务器2
              → 应用服务器3
```

典型场景：统一入口、负载均衡、SSL 终结、静态资源服务。

---

## 三、负载均衡策略

当后端有多台服务器时，Nginx 需要决定把每个请求转发给哪台。这就是负载均衡策略。

### 轮询（Round Robin）

最简单、最常用的策略。请求逐一分配到各台服务器，雨露均沾。

```
upstream backend {
    server 192.168.1.10:8080;
    server 192.168.1.11:8080;
    server 192.168.1.12:8080;
}
```

请求1 → .10，请求2 → .11，请求3 → .12，请求4 → .10……循环往复。

### 加权轮询（Weighted Round Robin）

不是所有服务器性能都一样。给高配机器更高权重，让它承担更多请求。

```
upstream backend {
    server 192.168.1.10:8080 weight=3;   # 处理 3/5 的请求
    server 192.168.1.11:8080 weight=2;   # 处理 2/5 的请求
}
```

### IP 哈希（IP Hash）

根据客户端 IP 的哈希值，将同一 IP 的请求固定分配到同一台服务器。用于解决**会话保持**（Session Stickiness）问题——比如用户登录后 Session 存在服务器 A 的内存里，下一请求如果跑到服务器 B 就丢失登录状态了。

```
upstream backend {
    ip_hash;
    server 192.168.1.10:8080;
    server 192.168.1.11:8080;
}
```

不过现在更推荐用 Redis 集中管理 Session，而不是依赖 IP Hash 这种"黏性"方案。

### 动静分离

静态资源（HTML、CSS、JS、图片）不需要经过应用服务器处理。Nginx 可以直接从磁盘读取返回，效率极高。

```
location /static/ {
    root /var/www;            # 请求 /static/logo.png → 磁盘 /var/www/static/logo.png
    expires 30d;              # 浏览器缓存 30 天
}

location / {
    proxy_pass http://backend; # 动态请求转发到应用服务器
}
```

---

## 四、常用命令

Nginx 的控制全部通过 `nginx` 命令（通常是 `./nginx` 或 `nginx`，取决于安装方式）：

```bash
nginx                    # 启动
nginx -s stop            # 立即停止（强制关闭连接）
nginx -s quit             # 优雅停止（处理完当前请求再退出）
nginx -s reload           # 重新加载配置（最常用：改完配置后必须执行才能生效）
nginx -t                  # 测试配置文件语法是否正确
ps aux | grep nginx       # 查看 Nginx 进程
```

> `reload` 不会中断服务：Nginx 启动新的 worker 进程加载新配置，旧 worker 处理完现有请求后优雅退出。

---

## 五、核心配置结构

Nginx 配置文件（通常位于 `/etc/nginx/nginx.conf` 或 `/usr/local/nginx/conf/nginx.conf`）分三大块：

### 全局块

配置运行 Nginx 的用户组、worker 进程数、日志路径等。

```
user nobody;
worker_processes auto;    # 自动匹配 CPU 核心数
error_log /var/log/nginx/error.log;
```

### events 块

配置网络连接模型和最大连接数。

```
events {
    worker_connections 1024;  # 每个 worker 进程最大并发连接数
}
```

### http 块

最重要的部分，包含 upstream 定义和 server 配置。

```
http {
    # 定义上游服务器组（负载均衡池）
    upstream backend {
        server 127.0.0.1:8080 weight=1;
        server 127.0.0.1:8081 weight=2;
    }

    # HTTP 服务器（80 端口）
    server {
        listen 80;
        server_name example.com;

        # 静态资源直接由 Nginx 返回
        location / {
            root html;
            index index.html index.htm;
        }

        # /api 路径转发到应用服务器
        location /api {
            proxy_pass http://backend;
        }
    }

    # HTTPS 服务器（443 端口）
    server {
        listen 443 ssl;
        server_name example.com;

        ssl_certificate     /path/to/cert.pem;
        ssl_certificate_key /path/to/key.pem;

        location / {
            proxy_pass http://backend;
        }
    }
}
```

关键字段：

- `upstream`：定义一组后端服务器，名字自定义。后续 `proxy_pass http://名字` 引用
- `server`：定义一个虚拟主机。可以同时配置多个（不同域名、不同端口）
- `location`：路径匹配规则。`/` 匹配所有，`/api` 匹配 `/api` 开头的路径
- `proxy_pass`：将匹配到的请求转发到指定地址
- `weight`：权重，用于加权轮询

---

## 六、常见场景

### 场景一：前端 SPA + 后端 API

```
server {
    listen 80;
    server_name app.example.com;

    # API 请求转发到后端
    location /api/ {
        proxy_pass http://127.0.0.1:3000;
    }

    # 其他所有请求返回前端 SPA 的 index.html
    location / {
        root /var/www/spa/dist;
        try_files $uri $uri/ /index.html;   # SPA 路由的关键配置
    }
}
```

### 场景二：多域名指向不同应用

```
server {
    listen 80;
    server_name blog.example.com;
    location / {
        proxy_pass http://127.0.0.1:4000;
    }
}

server {
    listen 80;
    server_name admin.example.com;
    location / {
        proxy_pass http://127.0.0.1:5000;
    }
}
```

同一个 80 端口，根据请求头中的 Host 字段分发到不同应用。

---

## 总结

Nginx 的角色可以概括为**流量调度中心**：

- 接收所有外部请求
- 静态的？直接返回
- 动态的？转发给后端
- 后端有多个？按负载均衡策略分发

架构越复杂，Nginx 的价值越大。它把"请求该去哪"这件事从应用代码里抽离出来，让应用只需关注业务逻辑。

---

*本文整理自个人学习笔记，力求用通俗语言讲清核心概念。如有疏漏，欢迎指正。*
