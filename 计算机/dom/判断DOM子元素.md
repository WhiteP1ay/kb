---
tags:
  - 面试
  - DOM
  - JavaScript
created: "2026-07-02"
---

# 判断一个元素是否是另一个元素的子元素

## 是什么

两种方式：while 循环沿 parentNode 链查找，或直接用 `node.contains(otherNode)` 一行搞定。

## 关键点

- `node.contains(otherNode)` → 标准 API，一行代码，包括自身
- 手动遍历：沿 parentNode 一直往上，遇到目标元素或 null 为止
- contains 比手动遍历更可靠：处理了跨文档节点等边界情况

## 相关笔记

[[原型链]]
