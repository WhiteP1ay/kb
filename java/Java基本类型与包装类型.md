---
tags:
  - Java
created: "2026-10-14"
---

# Java 基本类型与包装类型

## 是什么

Java 将核心数据分为**基本类型（Primitive）**与**包装类型（Wrapper）**两套体系。基本类型直接在栈内存存储原始裸数据，性能最高；包装类型是堆内存中的对象引用，具备对象的完整生命周期。

| 数据类型 | 基本类型（裸数据） | 包装类型（引用对象） | TS 对标 |
| :--- | :--- | :--- | :--- |
| **整数** | `int` (32位) | `Integer` | `number` |
| **长整型** | `long` (64位) | `Long` | `bigint` / `number` |
| **双精度浮点** | `double` | `Double` | `number` |
| **布尔** | `boolean` | `Boolean` | `boolean` |
| **字符** | `char` | `Character` | `string` |

## 核心差异

1. **能否为 null**：
   - 基本类型**绝不可赋为 `null`**（类成员默认零值，如 `int` 为 `0`，`boolean` 为 `false`；局部变量不赋初值会编译报错）。
   - 包装类型是对象引用，未初始化或缺失时**可以为 `null`**。
2. **泛型集合限制**：
   - Java 集合（泛型）只能容纳引用类型。`List<Integer>` 合法，`List<int>` 编译直接报错。
3. **自动装箱与拆箱（Autoboxing / Unboxing）**：
   - 编译器会自动在两者间转换：
     ```java
     Integer obj = 100; // 自动装箱：Integer.valueOf(100)
     int val = obj;     // 自动拆箱：obj.intValue()
     ```

## 陷阱

- **隐式拆箱空指针（NPE）**：当把包装类赋值给基本类型，或在表达式中与基本类型运算时，如果包装类为 `null`，运行期会直接抛出 `NullPointerException`：
  ```java
  Map<String, Integer> map = new HashMap<>();
  int count = map.get("unknown"); // ❌ map.get 返回 null，拆箱为 int 时抛 NPE！
  ```
  **防范规则**：业务读写 map 或 DTO 字段时，若可能为空，局部变量一律声明为 `Integer`，并先判空再使用。

## 相关笔记

- [[Java常用集合操作]]
- [[Java对象等价性与同一性]]
