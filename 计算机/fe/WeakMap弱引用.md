---
tags:
  - JavaScript
  - 面试
created: "2026-07-02"
---

# WeakMap 为什么键不能是基本类型

## 是什么

WeakMap 设计目的是存放临时数据避免内存泄漏——键是弱引用、不可枚举。基本类型不存在「引用」概念，无法实现弱引用。

## 关键点

- 键必须是对象：只有对象有引用，GC 才能追踪
- 弱引用：如果没有其他地方引用这个 key，GC 可以回收它（对应的 value 也会清掉）
- 用途：封装私有属性、缓存 DOM 节点元数据、防止内存泄漏
- 与 Map 区别：WeakMap 不可遍历、不可获取 size、没有 clear() 方法

## 相关笔记

[[Proxy-vs-defineProperty]]
