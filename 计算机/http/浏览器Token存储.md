---
tags:
  - 面试
  - HTTP
  - 认证
  - 浏览器
created: "2026-07-22"
---

# 浏览器 Token 存储

## SPA 常见方案

```text
Access Token → JavaScript 内存
Refresh Token → HttpOnly Cookie
```

Access Token 只保存在内存中，页面刷新后通过 Refresh Token 重新获取。

Refresh Token 不建议放入 `localStorage`。同源 JavaScript 可以直接读取 `localStorage`，发生 XSS 后，攻击者可能窃取长期凭证。

## BFF 方案

更安全的 BFF/服务端会话方案是：

```text
Access Token 和 Refresh Token → 服务端保存
浏览器 → 只保存不透明的 HttpOnly Session Cookie
```

如果将 Access Token 也放入 Cookie，浏览器会自动携带它，因此需要同时防御 CSRF。

`HttpOnly` 能防止 JavaScript 读取 Cookie，但不能阻止 XSS 在当前页面发起自动携带 Cookie 的请求。

## 参考资料

- [OWASP：HTML5 Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html)
- [OWASP：Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

## 相关笔记

[[Token刷新流程]] [[Cookie安全属性]] [[Fetch-credentials]]
