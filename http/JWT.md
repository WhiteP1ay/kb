---
tags:
  - HTTP
  - 面试
created: "2026-07-22"
---

# JWT

## 是什么

JWT（JSON Web Token）是一种使用 JSON Claims 表达身份和权限信息的 Token 格式。

常见的 JWT Access Token 由三部分组成：

```text
Header.Payload.Signature
```

- Header：Token 类型和签名算法
- Payload：用户标识、权限、过期时间等 Claims
- Signature：保证 Header 和 Payload 没有被篡改

Header 和 Payload 只是 Base64URL 编码，拿到 Token 的人通常都可以解码查看，因此不能在 Payload 中保存密码、密钥、身份证号等敏感信息。

```

签名原理

签名大致可以理解为

Signature = Sign(
  Base64Url(Header) + "." + Base64Url(Payload),
  密钥
)
```

## 相关笔记

[[Token刷新流程]] [[浏览器Token存储]] [[Refresh-Token-Rotation]] [[Cookie安全属性]]
