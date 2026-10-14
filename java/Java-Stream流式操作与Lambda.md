---
tags:
  - Java
created: "2026-10-14"
---

# Java Stream 流式操作与 Lambda

## 是什么

Java 8 引入的 Stream API 与 Lambda 表达式，为 Java 带来了声明式的函数式编程能力。它将集合（List/Set 等）视为数据流水线，通过链式管道完成过滤、转换、聚合与分组。

## 核心语法与机制

### 1. Lambda 语法与三大函数式接口
Java 使用单箭头 `->`（TS 是 `=>`），底层基于单抽象方法接口（SAM）：

- **`Predicate<T>`**：`t -> boolean`（条件过滤，给 `filter` 用）
- **`Function<T, R>`**：`t -> r`（数据转换映射，给 `map` 用）
- **`Consumer<T>`**：`t -> void`（纯副作用消费，给 `forEach` 用）
- **方法引用语法糖 `::`**：如 `String::toUpperCase` 等价于 `s -> s.toUpperCase()`。

> **目标类型推导（Target Typing）**：单独声明变量时必须显式标注类型（如 `Predicate<User> p = ...`）；但在作为方法实参（如 `filter(...)`）传入时，编译器根据形参插槽类型与集合泛型自动推导出入参类型与目标接口，可省去类型声明。

### 2. 中间操作 vs 终端操作（惰性求值）
- **中间操作（Intermediate）**：`filter()`, `map()`, `distinct()`, `sorted()`, `limit()`。
  - **惰性求值（Lazy）**：只挂号规则，不立即触发执行。
- **终端操作（Terminal）**：`toList()`, `collect()`, `count()`, `reduce()`, `forEach()`。
  - 只有调用终端操作时，流水线才真正启动遍历。Stream 是一次性的，终端操作执行后该流即关闭。

### 3. Java 16+ `.toList()` vs `.collect(Collectors.toList())`
- **`.toList()`**（Java 16+ 引入）：返回只读/不可变列表，代码最简练。
- **`.collect(Collectors.toList())`**：返回传统的标准可变列表（可后续调用 `.add()`）。

### 4. `Collectors.groupingBy`（分组聚合心智模型）
如同快递分拣：将扁平的 `List<T>` 按照某一个分类标准，自动归拢并组装为 `Map<分类Key, List<T>>`。
```java
// 按类型分组：产物是 Map<String, List<Item>>
Map<String, List<Item>> byType = items.stream()
    .collect(Collectors.groupingBy(Item::getType));
```

## 典型代码示例

```java
// 过滤、映射打折并求和
int total = orders.stream()
    .filter(item -> item >= 100)
    .map(item -> item * 9 / 10)
    .reduce(0, (a, b) -> a + b);
```

## 陷阱

- **缺少终端操作**：写了 `.stream().filter(...)` 但漏写 `.toList()` 或 `.collect()`，代码根本不会被执行。
- **不可变列表修改**：对 Java 16+ 的 `.toList()` 结果调用 `.add()` 会抛出 `UnsupportedOperationException`。

## 相关笔记

- [[Java常用集合操作]]
- [[Java基本类型与包装类型]]
- [[Java对象等价性与同一性]]
