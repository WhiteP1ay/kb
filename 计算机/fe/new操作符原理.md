---
tags:
  - JavaScript
  - 面试
created: "2026-07-02"
---

# new 操作符做了什么

## 是什么

四步：创建空对象 → 链接原型 → 执行构造函数绑定 this → 返回对象。

## 手写实现

```js
function myNew(Constructor, ...args) {
  // 1. 创建空对象，并链接原型
  const obj = Object.create(Constructor.prototype);

  // 2. 执行构造函数，绑定 this
  const result = Constructor.apply(obj, args);

  // 3. 如果构造函数返回了对象，返回它；否则返回 obj
  return result instanceof Object ? result : obj;
}
```

## 关键点

- `Object.create(Constructor.prototype)` 一步完成创建对象 + 链接原型
- 构造函数返回原始值时忽略，返回对象时取代 new 创建的对象
- 箭头函数没有 prototype，不能作为构造函数

## 相关笔记

[[原型链]] [[instanceof原理]]
