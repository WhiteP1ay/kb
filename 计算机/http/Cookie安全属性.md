---
tags:
  - 面试
  - HTTP
  - Cookie
  - 安全
created: "2026-07-22"
---

# Cookie 安全属性

Refresh Token Cookie 示例：

```http
Set-Cookie: refresh_token=...; HttpOnly; Secure; SameSite=Lax; Path=/auth/refresh; Max-Age=2592000
```

| 属性 | 作用 |
|---|---|
| `HttpOnly` | 禁止 JavaScript 通过 `document.cookie` 读取 |
| `Secure` | 只通过 HTTPS 发送 |
| `SameSite` | 控制跨站请求是否携带 Cookie |
| `Path` | 限制 Cookie 随哪些路径发送 |
| `Domain` | 指定可以接收 Cookie 的域；省略时为 Host-only |
| `Max-Age` | 指定从当前开始的存活秒数 |
| `Expires` | 指定绝对过期时间 |

## SameSite

- `Strict`：跨站请求不携带，限制最严格
- `Lax`：兼顾安全性和常见页面导航
- `None`：允许跨站携带，必须同时设置 `Secure`，并额外防御 CSRF

涉及 Cookie 的状态修改接口还应检查 `Origin`/`Referer`，必要时使用 CSRF Token。

## Cookie 名称前缀

`__Secure-` 要求 Cookie 从安全来源设置并包含 `Secure`：

```http
Set-Cookie: __Secure-refresh_token=...; Secure; HttpOnly
```

`__Host-` 要求包含 `Secure`、不能设置 `Domain`，且 `Path` 必须为 `/`：

```http
Set-Cookie: __Host-refresh_token=...; Secure; HttpOnly; Path=/
```

`__Host-` 能锁定具体主机，但不能同时把 `Path` 限制为 `/auth/refresh`。

## 更新与删除

Token Rotation 时由服务端使用新的 `Set-Cookie` 覆盖旧值。删除 Cookie 时，`Path` 和 `Domain` 必须与原 Cookie 匹配：

```http
Set-Cookie: refresh_token=; HttpOnly; Secure; SameSite=Lax; Path=/auth/refresh; Max-Age=0
```

## 参考资料

- [HTTP Cookie 规范](https://httpwg.org/http-extensions/draft-ietf-httpbis-rfc6265bis.html)
- [OWASP：Cross-Site Request Forgery Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)

## 相关笔记

[[浏览器Token存储]] [[Fetch-credentials]] [[Refresh-Token-Rotation]]
