# useSyncExternalStore 到底解决了什么问题

`useSyncExternalStore` 这个名字，第一次看几乎自带劝退效果。

它像一个只有“造状态管理库的人”才会接触的底层 API，所以很多 React 开发者翻到文档这里就关掉了页面。

但如果你想理解 React 18 之后“外部状态”为什么不能再像以前那样随便接，这个 hook 其实特别关键。

## 先说结论：它解决的是并发渲染下的数据撕裂

React 18 之后，渲染不再默认是一口气做完的同步过程。

React 可以：

- 渲染到一半先暂停
- 去处理更高优先级的任务
- 过一会儿再回来继续渲染

这就是并发渲染带来的灵活性。好处是页面不容易因为一次大更新卡死。

但问题也随之出现：如果组件读的是 React 之外的外部 store，那么“同一轮界面渲染”里，不同组件可能读到不同版本的数据。

这就叫 tearing，也就是“撕裂”。

## 什么叫撕裂

假设页面上有两个组件 A 和 B，都从同一个外部 store 读取主题色。

原本它们都应该显示“蓝色”。

但渲染过程可能变成这样：

1. React 先渲染组件 A，A 读到旧值“蓝色”
2. 渲染被打断
3. 这段时间外部 store 变成了“绿色”
4. React 恢复渲染组件 B，B 读到新值“绿色”

结果就是：同一个页面、同一块界面里，A 和 B 显示出了两个版本的状态。

用户看到的不是“渐进更新”，而是“逻辑分裂”。

## 为什么旧时代那套 `useEffect + useState` 不够了

过去很多人封装外部 store，习惯这样写：

```tsx
function useStore(store) {
  const [state, setState] = useState(store.getState());

  useEffect(() => {
    const unsubscribe = store.subscribe(() => {
      setState(store.getState());
    });
    return unsubscribe;
  }, [store]);

  return state;
}
```

在 React 17 以及更早的同步渲染时代，这种模式大多数时候没问题。因为组件树通常会一口气渲染完，外部 store 的变化和 React 的渲染节奏不太容易错位。

但到了并发时代，这种方案的问题就暴露了：

- 订阅是在 `effect` 里做的
- `effect` 发生在渲染提交之后
- React 渲染过程却可能中断、恢复、重试

也就是说，React 并不知道你和外部 store 之间到底发生了什么。它管自己的调度，store 管自己的变化，中间缺了一根“官方数据线”。

`useSyncExternalStore` 的出现，就是为了补上这根线。

## 它是怎么工作的

它的接口很简单：

```tsx
const snapshot = useSyncExternalStore(
  subscribe,
  getSnapshot,
  getServerSnapshot?
);
```

你可以把它理解成把两件关键事情正式交给 React：

- 怎么订阅外部 store
- 什么时候读取当前快照

核心思想是：在渲染阶段同步读取快照，而不是等渲染结束后再靠 effect 补救。

这意味着 React 能够在渲染过程中直接知道“当前外部状态是什么”。如果渲染期间 store 变了，React 会发现这次渲染用的快照已经过期，然后干脆重来一轮完整渲染。

代价是可能多渲染一次。

收益是不会出现“上半屏旧数据、下半屏新数据”的撕裂。

## 这里的 “Sync” 不是让页面变卡

很多人一看到 `Sync` 就误会，以为这意味着阻塞渲染、放弃并发。

其实不是。

这里的“同步”，说的是“读取快照的时机必须同步且一致”，不是说 React 整个调度过程要退回老式同步模式。

你可以这样理解：

- 调度层依然可以并发，React 仍然可以暂停和恢复渲染
- 但在某一轮具体渲染里，React 要保证组件看到的是同一个版本的外部状态

也就是说，它并没有取消并发，而是在并发条件下补上了数据一致性。

## 为什么 Zustand、Redux 这类库会碰到它

如果一个状态来自 React 组件内部的 `useState`，React 天然知道怎么调度它。

但像 Redux、Zustand、甚至一个你自己写的全局 store，本质上都属于“React 外部”的状态源。React 不会天然理解它们的变化节奏。

所以现代状态库要想在 React 18 的并发模型下表现稳定，就需要通过 `useSyncExternalStore` 这类官方机制，把外部状态系统接回 React 的调度体系里。

这也是为什么它看起来底层，却并不边缘。你未必每天手写它，但你很可能每天都在间接依赖它。

## SSR 里为什么还多了个 `getServerSnapshot`

服务端渲染又多一层复杂度：服务端没有浏览器环境，像 `localStorage`、`window` 这类对象都不存在。

所以 `useSyncExternalStore` 提供了第三个参数 `getServerSnapshot`，专门给服务端渲染时使用。

它的关键要求是：服务端初始快照和客户端首次水合时看到的快照，最好保持一致。

否则页面 HTML 明明已经输出了，客户端接管时却发现状态不同，就会触发 hydration mismatch。

所以这个参数看起来可选，实际上在 SSR 场景里很重要。

## 结语

`useSyncExternalStore` 表面上是一个底层 hook，实质上是在告诉开发者一件事：

外部 store 当然可以存在，但在并发 React 里，什么时候读、读到哪个版本、如何避免撕裂，必须由 React 自己来协调。

它不负责帮你设计 store，也不负责帮你写业务逻辑。它只是非常克制地解决了一个关键问题：让 React 在面对外部状态时，依然能保证渲染结果一致。

如果说并发渲染让 React 变得更灵活，那么 `useSyncExternalStore` 就是在提醒我们，灵活的前提，是别把数据一致性弄丢。

