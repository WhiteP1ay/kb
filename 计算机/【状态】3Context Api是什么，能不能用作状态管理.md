# Context API 是什么，能不能用作状态管理？

很多人第一次学 React 的 Context API 时，都会有一种感觉：这不就是官方版全局状态吗？

这个理解不能说完全错，但如果把 Context 直接等同于“状态管理方案”，后面大概率会越用越别扭。

更准确地说，Context API 首先是 React 为了解决 `props drilling` 提供的一种“跨层传值机制”。

## Context 最初要解决什么问题

所谓 `props drilling`，就是属性逐层透传。

例如页面有一个 `theme`，表示当前是深色模式还是浅色模式。这个值可能很多组件都关心，但它们又不一定都和顶层组件直接相连。

如果不用 Context，数据可能要一层层传下去：

1. 页面组件拿到 `theme`
2. 传给布局组件
3. 布局组件再传给面板组件
4. 面板组件再传给按钮组件

最后真正需要它的按钮，只是想知道自己该渲染成深色还是浅色，却被迫让中间每一层都参与“搬运”。

这就是 Context 出场的原因：别一层层递了，需要的人自己拿。

## 所以它能不能做状态管理

能，但要分场景。

如果项目比较小，或者你要共享的是一些更新频率不高、天然具有全局性的值，比如：

- 主题
- 当前语言
- 当前登录用户
- 配置项

那 Context 完全可以胜任。

问题在于，很多人看到它能共享，就顺手把越来越多的业务状态都塞进去，比如表单状态、列表筛选状态、弹窗开关、计数器、接口数据，最后把 Context 用成一个无约束的全局仓库。

这时它的问题就会慢慢冒出来。

## Context 的核心代价：变化会牵动消费者一起重渲染

Context 最常被拿出来说的缺点，就是性能特征比较粗。

看一个简单例子：

```tsx
const StoreContext = createContext({
  bears: 0,
  honey: 10,
  fish: 5,
});

function Provider({ children }) {
  const [state, setState] = useState({
    bears: 0,
    honey: 10,
    fish: 5,
  });

  return (
    <StoreContext.Provider value={{ state, setState }}>
      {children}
    </StoreContext.Provider>
  );
}

function BearCounter() {
  const { state } = useContext(StoreContext);
  return <div>{state.bears}</div>;
}
```

`BearCounter` 明明只关心 `bears`，但如果 `honey` 或 `fish` 变化了，Provider 的 `value` 也变了，它依然会跟着重新执行。

这不是 Context 的 bug，而是它的设计取向。它本来就是用来传播值的，不是天生为了做精细化订阅。

## 所以它更像“传递机制”，而不是“完整状态管理系统”

这句话很重要。

Context 很擅长做的是：

- 把某个值跨层传下去
- 让一组组件共享同一份上层信息

但它不擅长天然解决这些问题：

- 如何只订阅局部字段
- 如何避免不必要重渲染
- 如何追踪复杂状态变化来源
- 如何组织越来越膨胀的共享状态

一旦业务变复杂，你就会发现 Context 不够“精细”。

## 如果我就想用 Context，该怎么少踩坑

当然不是说 Context 不能用，而是最好用得克制一点。

比较实用的思路有三条。

### 1. 按职责拆分 Context

别把所有七大姑八大姨都扔进一个 Context 里。

比如主题、语言、用户信息、弹窗控制，如果硬塞进同一个 Provider，那任何一个字段变化都可能扩大影响面。

拆分后，边界会清楚很多。代价是 Provider 会增多，但至少复杂度是显式的。

### 2. 给 Provider 的 value 做稳定化

如果 `value` 每次 render 都创建新对象，那么下游消费者更容易被连带更新。

通常可以配合 `useMemo` 稍微稳一下：

```tsx
const value = useMemo(() => ({ count, setCount }), [count]);

return <MyContext.Provider value={value}>{children}</MyContext.Provider>;
```

它不能把 Context 变成精细订阅系统，但能减少一些因为引用变化导致的重复更新。

### 3. 不要因为懒得传 props 就把一切都升级成 Context

有些状态本来只在一小块局部功能里有意义，结果为了“少传几层”，直接提升到 Context，最后反而让边界更模糊。

能局部解决的状态，优先局部解决。Context 应该解决“跨层共享确实痛苦”的问题，而不是单纯替代正常的数据流设计。

## 社区状态库是怎么补这块短板的

像 Zustand 这样的库，本质上就在做一件 Context 不擅长的事：精细订阅。

例如组件可以只订阅自己真正关心的字段：

```tsx
const bears = useBearStore((state) => state.bears);
const honey = useBearStore((state) => state.honey);
```

这样一来，`bears` 变化时，不需要所有消费者都跟着刷新；只有订阅了 `bears` 的组件才会被影响。

这类能力背后，通常依赖的是更底层的订阅机制，比如 React 18 之后常见的 `useSyncExternalStore`。

所以你也可以这么理解：

- Context 解决的是“值怎么传过去”
- 状态库解决的是“值变化后谁该更新、谁不该更新”

两者有关，但不是一回事。

## 结语

Context API 当然能用来做状态共享，甚至在小项目里完全够用。

但它最适合扮演的角色，依然是“跨层传值工具”，而不是默认的全局状态中心。

如果共享的数据少、变化不频繁、影响范围明确，用 Context 很顺手。

可一旦你开始在意订阅粒度、性能、可追踪性和状态边界，就该意识到：Context 能帮你起步，但未必适合一个越来越复杂的应用跑完全程。

