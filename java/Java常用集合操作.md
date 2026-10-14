---
tags:
  - Java
created: "2026-10-14"
---

# Java 常用集合操作

## 是什么

Java 集合框架（`java.util.*`）是后端数据组织与流转的基础。与 TypeScript 使用原生数组和对象字面量不同，Java 集合严格遵循**面向接口编程**与**泛型类型约束**。

| 场景 | TS 结构 | Java 常用接口与具体实现 | 常见操作 |
| :--- | :--- | :--- | :--- |
| **有序列表** | `string[]` | `List<String> list = new ArrayList<>();` | `list.add("a");`<br>`list.get(0);`<br>`list.size();` |
| **键值映射** | `Record<string, number>` | `Map<String, Integer> map = new HashMap<>();` | `map.put("k", 1);`<br>`map.get("k");`<br>`map.containsKey("k");` |
| **去重集合** | `Set<string>` | `Set<String> set = new HashSet<>();` | `set.add("a");`<br>`set.contains("a");` |

## 关键点

1. **接口声明，实现类实例化**：
   规范写法永远是左侧声明为通用接口，右侧声明为具体实现类：
   ```java
   List<String> items = new ArrayList<>();
   Map<String, Object> data = new HashMap<>();
   ```
2. **方法调用取代方括号语法**：
   Java 集合不是语言内置语法糖，所有读写都必须通过成员方法：
   - 读列表：`items.get(i)`（禁止写 `items[i]`）
   - 写字典：`map.put(key, val)`（禁止写 `map[key] = val`）
   - 获取长度：`items.size()`（数组是 `.length`，集合是 `.size()`，字符串是 `.length()`）
3. **增强 for 循环（Foreach）**：
   - TS: `for (const x of items)`
   - Java: `for (String x : items)`（使用冒号 `:` 分隔元素与容器）

## 现代惯用法

1. **快速创建不可变集合（Java 9+）**：
   ```java
   List<String> list = List.of("apple", "banana");
   Map<String, Integer> map = Map.of("a", 1, "b", 2);
   ```
   > 注：`List.of()` 生成的集合不可变，调用 `.add()` 会抛出 `UnsupportedOperationException`。
2. **统计频次一行流（`getOrDefault` / `merge`）**：
   ```java
   // 传统写法：
   Integer count = map.get(w);
   map.put(w, count == null ? 1 : count + 1);

   // 现代简洁写法：
   map.put(w, map.getOrDefault(w, 0) + 1);
   ```

## 陷阱

- **后置自增作为实参传递（`temp++`）**：
  在 Java 中，`map.put(w, temp++)` 会先将未自增的旧值传入 `put`，事后才对局部变量自增，导致更新失败。累加计算务必使用 `temp + 1`。

## 相关笔记

- [[Java基本类型与包装类型]]
- [[Java对象等价性与同一性]]
