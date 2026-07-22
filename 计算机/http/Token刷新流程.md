---
tags:
  - 面试
  - HTTP
  - 认证
created: "2026-07-22"
---

# Token 刷新流程

Access Token 通常有效期较短，过期后使用 Refresh Token 换取新的 Access Token：

```text
客户端调用业务接口
→ Access Token 过期，服务端返回 401
→ 客户端调用专用刷新接口
→ 浏览器携带 Refresh Token
→ 服务端验证 Refresh Token
→ 签发新的 Access Token
→ 客户端重试原业务请求
```

服务端应检查 Refresh Token：

- 是否真实有效、未被篡改
- 是否过期或已被吊销
- 对应用户和登录会话是否仍然有效
- 是否属于当前客户端或设备
- 是否发生旧 Token 重放

Refresh Token 无效时，客户端应清除登录状态并重新登录，不能无限刷新或重试。

## 相关笔记

[[浏览器Token存储]] [[Refresh-Token-Rotation]] [[Fetch-credentials]]
