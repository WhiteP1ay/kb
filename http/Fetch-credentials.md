---
tags:
  - HTTP
  - 面试
created: "2026-07-22"
---

# Fetch credentials

`fetch` 的 `credentials` 控制浏览器是否在请求中自动携带 Cookie 等凭证，以及是否接收响应设置的 Cookie。

| 取值 | 含义 |
|---|---|
| `omit` | 不携带凭证，也不接收响应设置的 Cookie |
| `same-origin` | 仅同源请求携带，默认值 |
| `include` | 同源和跨源请求都允许携带 |

跨源调用 Refresh Token 接口：

```javascript
await fetch("https://api.example.com/auth/refresh", {
  method: "POST",
  credentials: "include",
});
```

JavaScript 仍然读不到 HttpOnly Cookie，浏览器会自动生成 `Cookie` 请求头。

服务端 CORS 响应需要：

```http
Access-Control-Allow-Credentials: true
Access-Control-Allow-Origin: https://app.example.com
```

允许凭证时，`Access-Control-Allow-Origin` 不能使用 `*`。

`credentials: "include"` 只是允许携带，不能绕过 Cookie 的 `Domain`、`Path`、`Secure`、`SameSite`、有效期和浏览器第三方 Cookie 策略。

## 参考资料

- [WHATWG Fetch Standard](https://fetch.spec.whatwg.org/#requestcredentials)

## 相关笔记

[[Token刷新流程]] [[浏览器Token存储]] [[Cookie安全属性]]
