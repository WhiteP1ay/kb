---
tags:
  - Java
created: "2026-10-14"
---

# Java 对象等价性与同一性

## 是什么

Java 区分两类相等性概念：
- **`==`（同一性，Identity）**：比对两个变量保存的**内存地址（是否指向堆中的同一个对象实例）**。
- **`.equals()`（等价性，Equality）**：比对两个对象内部的**数据/属性值是否逻辑一致**。

## 为什么 TS 开发者容易踩坑

在 TypeScript 中，`string` 是基本类型（Primitive），`"hello" === "hello"` 默认按字面内容逐字符对比。

但在 Java 中，`String` 是**对象（`java.lang.String`）**。如果通过输入流、数据库读取或 `new String(...)` 创建字符串，它们分布在不同的堆内存地址上。使用 `==` 比对两个字符串永远为 `false`，必须使用 `.equals()` 对比内容。

```java
String a = new String("hello");
String b = new String("hello");

a == b;      // false（两块独立内存）
a.equals(b); // true （字符序列完全相同）
```

## 关键点

1. **基本类型只用 `==`**：
   - 基本类型（`int`, `double` 等）不是对象，没有方法，直接用 `1 == 1` 对比数值。
2. **所有对象比内容必须用 `.equals()`**：
   - 字符串、日期（`LocalDate`）、BigDecimal、业务实体等，比值一律用 `.equals()`。
3. **安全调用习惯（防 NPE）**：
   - 当与字面常量比较时，将常量写在前面：
     ```java
     "admin".equals(role); // 推荐：即使 role 为 null 也安全返回 false
     role.equals("admin"); // 风险：若 role 为 null 直接抛 NullPointerException
     ```
   - 或使用工具方法：`Objects.equals(a, b)`。

## 陷阱

- **Integer 缓存池陷阱**：
  Java 为 `-128 ~ 127` 范围内的 `Integer` 建立了常量池（`IntegerCache`）：
  ```java
  Integer x = 100;
  Integer y = 100;
  x == y; // true（命中缓存池同一个对象）

  Integer p = 1000;
  Integer q = 1000;
  p == q; // false（超出缓存，堆中两个独立对象！）
  ```
  **铁律**：包装类型只要比较数值，永远使用 `.equals()`，绝不可偷懒使用 `==`。

## 相关笔记

- [[Java基本类型与包装类型]]
- [[相等与身份比较]]
- [[Object.is-vs-严格相等]]
