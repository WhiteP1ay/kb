---
tags:
  - JavaScript
  - 面试
created: "2026-07-02"
---

# this 指向的四种场景

## 是什么

this 的值取决于函数调用方式，不是定义位置（箭头函数除外）。四种场景：直接调用、对象方法、new 构造、call/apply/bind。

## 关键点

- 直接调用 foo()：严格模式 undefined，非严格模式 window
- 对象方法 obj.foo()：指向 obj
- new Foo()：指向新创建的实例，详见[[new操作符原理]]
- call/apply/bind：指向第一个参数
- 箭头函数不遵循以上规则，this 固定为定义时外层作用域的 this

## 相关笔记

[[call-apply-bind]] [[普通函数与箭头函数]]
