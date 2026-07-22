---
tags:
  - HTTP
  - 浏览器
created: "2026-07-02"
---

# Content-Disposition 控制下载文件名

## 是什么

HTTP 响应头，控制内容是内联显示还是下载。`inline` 浏览器预览，`attachment` 触发下载。可指定 filename。

## 关键点

- `Content-Disposition: inline` → 浏览器预览（PDF 等）
- `Content-Disposition: attachment; filename="xxx.pdf"` → 触发下载并指定文件名
- 解决浏览器内置 PDF 阅读器接管后文件名不可控的问题
- 服务端设置，不依赖前端 `<a download>` 属性

## 相关笔记

[[disposition]]
