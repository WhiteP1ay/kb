# JS 面试题整理

---

## 原型继承和类继承的区别？

"原型继承"和 ES6 引入的"类继承"本质上是同一套机制的两种表现形式。

```js
function Animal(name) {
  this.name = name;
}

// 通过 prototype 属性定义方法
Animal.prototype.sayHi = function() {
  console.log(`Hi, I'm ${this.name}`);
};

// 继承实现需要三步: 1. call 2. create 3. constructor
function Dog(name, breed) {
  Animal.call(this, name); // 借用构造函数，继承属性
  this.breed = breed;
}
Dog.prototype = Object.create(Animal.prototype); // 原型继承方法
Dog.prototype.constructor = Dog; // 修复 constructor 指向

const dog = new Dog('Lucky', 'Labrador');
dog.sayHi(); // Hi, I'm Lucky
```

ES6 的 `class` 和 `extends` 是语法糖，本质仍然是基于原型链。class 定义的 methods 自动放在 prototype 上，extends 内部帮你做了 Object.create 那些事。

---

## 什么是 prototype、\_\_proto\_\_？

**prototype**：只有函数才有 prototype。当一个函数被用作**构造函数**（即通过 `new` 调用）时，prototype 属性用来为**实例对象提供共享的属性和方法**。

**\_\_proto\_\_**：所有对象都有该属性，指向对象原型，即构造函数的 prototype。

什么时候用：

- prototype 是函数（构造函数/class）才有的，用来存放共享的方法、属性
- 每个对象身上都有 `__proto__`，**指向构造函数的 prototype**（理解 instanceof 的关键）

> 举个例子：构造函数是造车工厂，prototype 是工厂提供的工具箱，每一辆车都是这套工具加工生产的。`__proto__` 是每辆车的内心发问："我是哪套工具产生的？"总是指向构造函数的 prototype。

---

## 原型链的指向是怎样的？

- `dog.__proto__` → `Dog.prototype`
- `Dog.prototype.__proto__` → `Animal.prototype`
- `Animal.prototype.__proto__` → `Object.prototype`
- 最终到 `null`

---

## instanceof 的实现原理？

```js
function myInstanceOf(obj, Constructor) {
  // 基本类型直接返回 false
  if (typeof obj !== 'object' || obj === null) return false;

  let proto = obj.__proto__;
  const prototype = Constructor.prototype;

  while (proto) {
    if (proto === prototype) return true;
    proto = proto.__proto__;
  }
  return false;
}
```

沿 `__proto__` 链逐个比对，跟构造函数的 `prototype` 对上就是。

---

## new 对象的时候发生了什么？

四步：

1. 创建一个新对象 `obj`
2. 把 `obj.__proto__` 指向构造函数的 `prototype`
3. 执行构造函数，绑定 `this` 为 `obj`，往 `obj` 上添加属性
4. 返回新对象（或构造函数返回的对象）

手写实现：

```js
function myNew(Constructor, ...args) {
  // 1. 创建空对象
  let obj = {};
  // 2. 连接原型链
  obj.__proto__ = Constructor.prototype;
  // 3. 执行构造函数
  let result = Constructor.apply(obj, args);
  // 4. 返回
  return result instanceof Object ? result : obj;
}
```

---

## 什么是闭包？

闭包本质是**函数对外部作用域变量的引用，延长作用域链**。

使用场景：

- 封装私有变量
- 在异步环境中"记住"某些状态
- 函数工厂（生成带记忆能力的函数）

```js
// 模拟私有变量
function Person(name) {
  let _age = 0; // 私有变量

  return {
    getName() {
      return name;
    },
    getAge() {
      return _age;
    },
    growUp() {
      _age++;
    }
  };
}

// 异步环境记住状态
for (var i = 1; i <= 3; i++) {
  (function(n) {
    setTimeout(() => {
      console.log("i =", n);
    }, 1000);
  })(i);
}
// 1秒后输出： i = 1, i = 2, i = 3

// 函数工厂
function createCounter(start = 0) {
  let count = start;
  return function() {
    count++;
    return count;
  };
}
const counter1 = createCounter(10);
const counter2 = createCounter(100);
```

---

## 什么是 IIFE？使用场景有哪些？

IIFE（立即执行函数表达式）的使用场景：

- 创建独立作用域，库用来隔离变量
- 立即运行一次逻辑（执行初始化代码）
- 模块化之前，通过 IIFE 封装库

---

## Boolean 和 boolean 的区别？

`boolean` 是原始数据类型，只有两个值 `true` 和 `false`。

`Boolean` 是 JavaScript 的一个**内置对象构造函数**，用于创建 Boolean 包装对象。但通常不推荐作为构造对象使用，更多是作为普通函数把任意值转成 boolean。

---

## `==` 的判断规则？

`x == y`，当二者类型相同时，直接按 `===` 来比较，但不完全等价：`NaN == NaN` 是 false。

规则：

- `Number == String` → 把 String 转成 Number
- `Boolean == Anything` → 把 Boolean 转成 Number
- `null == undefined` → 它们俩相等，且只彼此相等
- `Object == Primitive` → 对象通过 `valueOf()` 或 `toString()` 转为原始值，再比较
- `NaN == anything` → 永远是 false

注意：`==` 和 `if(value)` 判断逻辑不一致，因为 `if(value)` 不涉及隐式转换。

---

## `+` 运算符的隐式转换规则？

如果**任意一边是字符串**，优先转为字符串并拼接；否则尝试转数字。

---

## WeakMap 的键为什么不能是基本类型？

WeakMap 的设计目的是存放临时数据，避免内存泄漏。为了垃圾回收，它的键都是弱引用，且不可枚举。值类型不存在"引用"，因此无法实现弱引用。

---

## 如何用 WeakMap 封装私有属性？

```js
const _privateData = new WeakMap();

class Person {
  constructor(name) {
    _privateData.set(this, { name });
  }

  getName() {
    return _privateData.get(this).name;
  }
}

const p = new Person("Alice");
console.log(p.getName());
```

---

## 普通函数和箭头函数的区别？

**普通函数：**

- `this` 是动态绑定的
- 有自己的 `arguments` 对象（类数组）
- 可以作为构造函数被 `new` 调用
- 有 `prototype` 属性
- 可以作为生成器

**箭头函数：**

- `this` 指向定义时的上下文中的 `this`，自己没有 `this`
- 没有 `arguments`
- 不能作为构造函数
- 没有 `prototype`
- 不能作为生成器（不能有 `yield`）

---

## this 取值的四个场景？

| 调用方式 | this 指向 |
|----------|-----------|
| 直接调用 `foo()` | 严格模式：`undefined`；非严格模式：全局对象（浏览器中为 `window`） |
| 对象方法调用 `obj.foo()` | `this` 指向 `obj` |
| 构造函数调用 `new Foo()` | `this` 指向新创建的实例 |
| 通过 `call` / `apply` / `bind` 调用 | `this` 指向你传入的第一个参数 |

---

## call / apply / bind 的区别？

- **call**：调用函数，并指定 `this`，参数逐个传入
- **apply**：调用函数，并指定 `this`，参数以数组形式传入
- **bind**：不会立即调用，而是返回一个**新函数**，`this` 被永久绑定

call / apply 更多用于**方法借用**：`Array.prototype.slice.call(arguments)`

---

## TS 中 Exclude 和 Omit 的区别？

`Exclude<T, U>` 从联合类型 T 中排除 U 的成员。作用于**联合类型**。

`Omit<T, K>` 从对象类型 T 中删除 K 属性。作用于**对象类型**。

---

## 如何判断对象中是否存在循环引用？

```js
obj.self = obj;
```

方法 1：`JSON.stringify` 本身不能处理循环引用，会抛错，但可以配合 try-catch 侧面检测。

方法 2：Set 不能存储重复的值。用 WeakSet 遍历对象属性，检测重复引用：

```js
function hasCycle(obj) {
  const seen = new WeakSet();

  function detect(value) {
    if (typeof value === 'object' && value !== null) {
      if (seen.has(value)) {
        return true; // 检测到循环
      }
      seen.add(value);
      for (let key in value) {
        if (detect(value[key])) {
          return true;
        }
      }
    }
    return false;
  }

  return detect(obj);
}
```

---

## JS 中有哪些方法可以退出循环？

| 方法 | 作用范围 | 效果 |
|------|----------|------|
| `break` | 循环 | 结束整个循环 |
| `continue` | 循环 | 跳过本次，进入下次迭代 |
| `return` | 函数内部循环 | 结束函数，循环自然退出 |
| `throw` | 任意 | 抛异常，中断循环 |
| `some` / `every` | 数组迭代方法 | 逻辑条件成立后退出迭代 |

---

## 如何使对象支持 `for...of` 迭代？

```js
obj[Symbol.iterator] = function* () {
  for (const key of Object.keys(this)) {
    yield [key, this[key]];
  }
};
```

---

## Proxy 和 Object.defineProperty 的区别？

`Object.defineProperty` 是给对象设置 get/set，`Proxy` 会代理整个对象，可以拦截多种操作。

| 特性 | Object.defineProperty | Proxy |
|------|----------------------|-------|
| 粒度 | 针对单个属性 | 针对整个对象 |
| 拦截范围 | 仅限 get / set | 支持 13 种操作（get、set、has、deleteProperty、ownKeys、apply、construct 等） |
| 新增/删除属性 | 无法拦截 | 可以拦截 |
| 数组索引/长度变化 | 无法拦截 | 可以拦截 |
| 继承/原型链操作 | 无法拦截 | 可以拦截 |
| 函数调用 | 无法拦截 | apply 可拦截函数调用 |
| 性能 | 高（更轻量） | 较低（代理更复杂） |

---

## `a.b.c.d` 和 `a['b']['c']['d']` 的区别？

**点语法**：要求每一级属性名必须是**合法的标识符**（字母、数字、下划线、美元符号，且不能以数字开头），属性名固定写死。

**方括号语法**：属性名是一个**字符串**，可以是任意字符，属性名可以是变量动态访问。

---

## `use strict` 是什么，有什么用？

`"use strict"` 是 JavaScript 的严格模式声明。启用后：

- 禁止使用未声明的变量
- 函数中的 `this` 不再指向全局对象（为 `undefined`）
- 禁止删除不可删除的属性
- 禁止重复的参数名
- eval 有自己的作用域

---

## 如何判断一个元素是否是另一个元素的子元素？

```js
let current = a.parentNode;
while (current) {
  if (current === b) {
    console.log("a 是 b 的子元素");
    break;
  }
  current = current.parentNode;
}
```

也可以用 `b.contains(a)`，一行搞定。

---

## CommonJS 的本质是什么？

通过了解 `require` 函数的实现可以彻底搞懂 CommonJS：

```js
const moduleCache = {};

function require(modulePath) {
  // 1. 解析模块的绝对路径
  const _filename = resolveModulePath(modulePath);

  // 2. 检查模块是否已存在于缓存中
  if (moduleCache[_filename]) {
    return moduleCache[_filename];
  }

  // 3. 真正运行模块代码的辅助函数
  function _require(exports, require, module, _filename, _dirname) {
    // 目标模块的代码将在这个函数内执行...
  }

  // 4. 准备参数
  const module = {
    exports: {}, // 模块的导出对象
  };

  const exports = module.exports;
  const _dirname = getDirName(_filename);

  // 5. 调用模块运行函数
  _require.call(exports, exports, require, module, _filename, _dirname);

  // 6. 缓存 module.exports
  moduleCache[_filename] = module.exports;

  // 7. 返回 module.exports
  return module.exports;
}
```

再面对修改 `module` / `module.exports` / `this` 的题，以及关于缓存的题都参考这个。
