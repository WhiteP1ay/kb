---
tags:
  - HTTP
  - 面试
created: "2026-07-22"
---

# Refresh Token Rotation

每次使用 Refresh Token 刷新时，服务端同时签发新的 Refresh Token，并立即使旧 Token 失效：

```text
使用 RT1 刷新
→ 签发新的 Access Token 和 RT2
→ RT1 失效
```

如果 RT1 之后再次被使用，说明它可能已经泄漏。服务端可将其识别为重放攻击，并吊销这一 Token family 对应的登录会话。

服务端需要保存 Refresh Token 之间的关系，才能在发现旧 Token 重放时定位并吊销当前有效的 Token。

## 多个请求同时过期

如果请求 A、B、C 同时使用 RT1 刷新：

```text
A 使用 RT1 成功，获得 RT2，RT1 失效
B 再使用 RT1，触发重放检测
→ 登录会话可能被整体吊销
```

前端应采用 single-flight：同时只发出一个刷新请求，其余请求等待同一个 Promise。

```javascript
let refreshPromise = null;

function refreshOnce() {
  if (!refreshPromise) {
    refreshPromise = refreshAccessToken().finally(() => {
      refreshPromise = null;
    });
  }

  return refreshPromise;
}
```

还应注意：

- 只对明确的 Token 过期错误触发刷新
- 刷新接口自身不能再次触发刷新
- 每个原请求最多自动重试一次
- 刷新失败后统一清除登录状态

## 参考资料

- [RFC 9700：Refresh Token Protection](https://www.rfc-editor.org/rfc/rfc9700.html#name-refresh-token-protection)

## 相关笔记

[[Token刷新流程]] [[浏览器Token存储]] [[Cookie安全属性]]
