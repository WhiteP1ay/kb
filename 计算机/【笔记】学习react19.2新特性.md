这篇文章“费曼一下”React 19.2的一些新特性

### `<Activity />` – react版本的keep-alive

`<Activity />`是一个新的内置组件，帮我们控制不可见部分（比如Tabs组件隐藏起来的部分）的行为。

使用体验类似于vue的v-show，我们可以用来隐藏不需要渲染的组件。

被隐藏的组件

- 通过调用cleanup来清理effect（比如事件绑定、持续存在的timer等），以减少不必要性能损耗
- 保持低优先级活跃，在浏览器空闲的时候静默更新ui
- 当切换至可见时，渲染成本更低，性能更好（比condition&，或display:none性能好）

这意味着在Tab页之间切时感受更丝滑——不再需要重新获取数据，也不会再丢失滚动位置。

相比于条件渲染：

```jsx
{activeTab === 'reports' && <Reports />}
{activeTab === 'settings' && <Settings />}
```

当切换Tab的时候:

- react会卸载旧组件，再挂载新的组件
- 旧组件的状态会丢失
- effect会重新执行

而使用Activity， 不再需要频繁的卸载挂载，也不需要手动重新设置状态。effect会暂停。

而相比于diaplay:none，同样是隐藏组件，但后台effect变得更方便可控。

另一个使用场景是预渲染。如果条件渲染的话，只有条件为true组件内部逻辑才会启动。而Activity，即使隐藏起来也可以默默在后台获取数据，减少用户等待时间（当然这个特性比较meta，在useEffect里fetch数据肯定不行）。

我的评价是早就该出了。keep-alive都多少年了react才想起来搞一个。

### `useEffectEvent` effect依赖数组的大救星

这东西在设计useEffect的时候不就应该考虑到吗。怎么才放出来。

举一个最简单的例子，假设WebSocket成功连接之后打印用户名字。

你就不得不把user.name放依赖数组里。

```jsx
useEffect(() => {
    const connection = createConnection();
    connection.on('message', (msg) => {
        console.log('New message for', user.name);
    });
    return() => connection.disconnect();
}, [user.name]);
```

这就意味着user.name改变（或者别的什么与WebSocket无关的，比如官方文档例子是页面主题Theme），就会导致WebSocket的重连、页面抖动。

现在可以通过useEffectEvent来保持effect稳定了。

```jsx
const onMessage = useEffectEvent((msg) => {
    console.log('New message for', user.name);
});

useEffect(() => {
    const connection = createConnection();
    connection.on('message', onMessage);
    return() => connection.disconnect();
}, []);
```

现在:

- effect只运行一次
- onMessage 总能拿到最新的user.name
- 减少了ESlint的报错

### `cacheSignal()` — 渐渐变成了next的模样

这个特性专服务器组件设计。

简单的说就是告诉用户cache()的生命周期何时结束，这样用户可以取消掉已经没必要的操作。比如过久的读取数据。

之前我们可以通过cache来缓存或者说记忆化fetch结果。

```jsx
import { cache } from'react';

const fetchPostMeta = cache(async (postId) => {
    console.log(`Fetching metadata for post ${postId}`);
    const res = awaitfetch(`https://api.example.com/posts/${postId}/meta`);
    return res.json();
});

asyncfunctionPost({ postId }) {
const meta = awaitfetchPostMeta(postId);
return (
<div>
    <h1>Post {postId}</h1>
    <p>Views: {meta.views}</p>
</div>
  );
}
```

如果用户在fetch完成之前离开页面，API请求仍然在后台运行。

这会导致服务器资源浪费。现在可以通过传给fetch一个signal，让它在cache失效时自动取消无效的请求。

```jsx
import { cache, cacheSignal } from'react';

const fetchPostMeta = cache(async (postId, { signal }) => {
console.log(`Fetching metadata for post ${postId}`);
const res = awaitfetch(`https://api.example.com/posts/${postId}/meta`, { signal });
return res.json();
});

asyncfunctionPost({ postId }) {
    const signal = cacheSignal();
    const meta = awaitfetchPostMeta(postId, { signal });
    return (
    <div>
        <h1>Post {postId}</h1>
        <p>Views: {meta.views}</p>
    </div>
    );
}
```

### 其他

还有一些特性也特别Meta，比如Partial Pre-rendering、批量处理Suspense等。

显然这些特性这不是给普通用户准备的，更多的是为Next这类框架提供支持。

Partial Pre-rendering（PPR）：简单的说，现在页面静态部分（比如header/footer）可以被预渲染和缓存了，动态部分可以等晚些再resume。

```ts
const result = awaitprerender(<Page />);
const html = renderToString(result.prelude);
```

React会先渲染静态部分（result.prelude），把这部分发送给浏览器，再等到动态数据获取完毕后把动态部分resumes。这样用户会立即看到内容，React会在后续无缝地填充其余的东西。

Batching Suspense Boundaries ：同样为SSR准备。

此前，Suspense部分会在不同时间渲染，导致页面抖动。现在它会等待一小段时间将多个Suspense组合在一起，让变更效果更自然。
