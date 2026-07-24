---
tags:
  - JavaScript
  - 面试
created: "2026-07-02"
---

# ESM 与 CommonJS 的区别

## 是什么

CommonJS 同步加载（require），运行时确定，值的拷贝。
ESM 静态（编译时确定），支持异步加载，值的引用，tree-shaking 友好。

## 关键点

- CJS：require 运行时加载，module.exports 导出，导出值的拷贝
- ESM：import 编译时静态分析，export 导出，导出值的引用（活绑定）
- ESM 支持 tree-shaking（因为静态结构），CJS 不行
- Node.js 中 .mjs 或 package.json type:"module" 启用 ESM
- 循环引用：CJS 返回不完整对象，ESM 通过活绑定解决

## CommonJS 实现原理（伪代码）

以下伪代码展示了 require 函数的内部工作机制。`_require` 函数体内的代码模拟了**用户在模块文件中可能写的各种导出方式**——目的是演示不同写法对最终 `module.exports` 的影响：

```javascript
function require(modulePath) {
  // 1. 根据模块路径，得到完整的绝对路径
  var moduleId = getModuleId(modulePath);

  // 2. 判断缓存
  if(cache[moduleId]) {
    return cache[moduleId];
  }

  // 3. 真正运行模块代码的辅助函数
  function _require(exports, require, module, __filename, __dirname) {
    // 目标模块的代码将在这里运行
    this.a = 1;           // this = exports，所以 module.exports.a = 1
    exports.b = 2;        // module.exports.b = 2
    exports = { c: 3 };   // 重赋值 exports 局部变量，断开与 module.exports 的关联
    module.exports = { d: 4 }; // module.exports 指向新对象
    exports.e = 5;        // 写到旧对象，不影响返回值
    this.i = 6;           // 同上，写到旧对象
  }

  // 4. 准备参数并运行
  var module = { exports: {} };
  var exports = module.exports;
  var __filename = getModuleId(modulePath);
  var __dirname = getDirname(__filename);
  _require.call(exports, _require, module, __filename, __dirname);

  // 5. 缓存并返回 module.exports
  cache[moduleId] = module.exports;
  return module.exports; // → { d: 4 }
}
```

### 关键结论

- `exports` 是 `module.exports` 的引用，初始指向同一个对象
- 对 `exports` **重新赋值**（`exports = ...`）会断开引用，`module.exports` 不受影响
- 对 `exports` **添加属性**（`exports.x = ...`）有效，因为操作的是同一个对象
- **最终导出的是 `module.exports`**，不是 `exports`

## 相关笔记
