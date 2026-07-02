---
tags:
  - HTTP
  - 网络
created: "2026-07-02"
---

# TCP 粘包问题

## 是什么

TCP 是字节流协议，不保留应用层消息边界。发送方两次 write 的数据可能在接收方被合并读取。HTTP 通过 Content-Length 和分块传输解决。

## 关键点

- TCP 只保证可靠、有序传输，不关心「一条消息」的边界
- HTTP 方案：请求行+Headers+空行(\r\n\r\n)标识头结束 + Content-Length 标识体长度
- 分块传输：Transfer-Encoding: chunked，每块前有长度标识
- WebSocket 等长连接协议需要在应用层自己定义帧格式
