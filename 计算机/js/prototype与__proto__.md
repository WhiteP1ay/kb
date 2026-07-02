---
tags:
  - 面试
  - JavaScript
  - 基础
created: "2026-07-02"
---

# prototype 与 __proto__ 的区别

## 是什么

prototype 是函数才有的属性，存放共享方法；__proto__ 是所有对象都有的，指向构造函数的 prototype。

## 关键点

- prototype：只有函数有，为实例提供共享属性和方法
- __proto__：所有对象有，总是指向构造函数的 prototype
- 比喻：构造函数=工厂，prototype=工具箱，__proto__=产品追溯「我是哪套工具产生的」

## 相关笔记

[[原型链]] [[new操作符原理]]
