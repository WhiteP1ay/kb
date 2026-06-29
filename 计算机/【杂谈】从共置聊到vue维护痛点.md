# 从共置聊到Vue维护痛点

## 何为共置

本文章的点子起源于读到了[这篇文章](https://kentcdodds.com/blog/colocation)，作者思考了一件我们司空见惯的事：为什么注释总是紧挨着代码？

为什么不是建一个巨大的 `README.md`，或者建一个跟 `src` 目录同级的 `docs` 目录？

作者给了三个观点：

- **同步性**：修改代码的时候方便同步修改注释，但如果二者分开的话，注释估计就慢慢失效了。
- **适用性**：代码和注释分开可能让人遗漏注释文档的存在，因为并不是每段代码都有注释。
- **易用性**：来回切文件，对着代码找注释文档真的很痛苦。

由此我们不禁思考，前端项目把 JS/HTML/CSS 拆分是否是好的实践？

似乎开发者很喜欢把不同的东西分分类，放进不同的目录下，并美名其曰：关注点分离。

现在你依旧能看到很多关注点分离的例子：

- 测试用例都放在 `test` 目录里
- 应用状态都放在 `store` 目录里 
- 干脏活的函数都在 `utils` 目录里

我不是说关注点分离的实践一定是坏的，把测试用例放到不常打开的目录下确实清净不少（不是）。

但这样拆分项目文件确实很容易造成大量的代码冗余：一个组件删除掉了，它引用过的状态可能仍然在 `store` 里，使用过的工具函数也还在 `utils` 目录下，CSS 样式文件中，大量的样式类堆在一起，哪些有用哪些没用也变得不重要了，后人只需要继续往上写新的样式类就好。

屎山越堆越高，你不敢删也不敢动，只能接着往上垒。像极了爱情。

## 进步

好在现代前端框架发现了关注点分离的缺点，并提出了组件的概念。

我们把分离开来的 HTML/CSS/JS 放在一个文件里，给它起了新名字"组件"。它像 class 一样，有入参、有属性、有方法、可以被复用，也可以被封装成库。

从此我们开开心心地把 UI 设计稿拆分成若干个组件，再把组件像搭积木一样搭建成完整的 UI，让状态在组件中流转，这就是我们早些年前端项目的实现逻辑。

但随着前端项目的业务逻辑越来越复杂，我们发现只围绕 UI 做抽象和复用已经不能满足需求了。我们还想复用逻辑。

所以后来 React 提出了 `react-hooks`，Vue 3 也紧随其后提出了"组合式函数"。我们看似把 UI 和逻辑拆分了，但逻辑拆分绝不是关注点分离，而是进一步的共置。

看看这段 React 的代码吧！

```tsx
// 代码来自 react-query 的 Overview

function Example() {
  const { isPending, error, data } = useQuery({})

  if (isPending) return 'Loading...'

  if (error) return 'An error has occurred: ' + error.message

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{' '}
      <strong>✨ {data.stargazers_count}</strong>{' '}
      <strong>🍴 {data.forks_count}</strong>
    </div>
  )
}
```

这个函数中，我们把 UI 和逻辑进一步地共置。不同逻辑分支返回不同 UI，极大提升了代码的可读性、可维护性。

但很遗憾，我个人觉得在 Vue 项目里我们很难做到如此逻辑分明。因为 Vue 组件本质还是分离思想，把组件分离成了 `<script>`/`<template>`/`<style>` 等几个部分。

让我们来试试：

```html
<template>
  <div v-if="isPending">Loading...</div>
  <div v-else-if="error">An error has occurred: {{ error.message }}</div>
  <div v-else>
    <h1>{{ data.name }}</h1>
    <p>{{ data.description }}</p>
    <strong>👀 {{ data.subscribers_count }}</strong>
    <strong>✨ {{ data.stargazers_count }}</strong>
    <strong>🍴 {{ data.forks_count }}</strong>
  </div>
</template>

<script setup>
import { useQuery } from '@tanstack/vue-query'; // Vue 版的 TanStack Query

// 逻辑部分（直接与模板共置，类似 React Hooks）
const { isPending, error, data } = useQuery({
  queryKey: ['repoData'],
  queryFn: () =>
    fetch('https://api.github.com/repos/TanStack/query').then((res) =>
      res.json()
    ),
});
</script>
```

我想知道 `isPending` 和 `error` 的作用，就不得不 template 和 script 两块来回跳，为什么 UI 不能紧挨着状态呢？（我知道 Vue 也可以用 JSX，但这不是主流，也不是重点。）

随着代码量逐渐变多，你一个屏幕恐怕不能同时放下 template 和 script 了。你为了看某个状态的作用又不得不来回滚动鼠标，关注点分离的毛病又都回来了。这就是我想说的 Vue 代码维护的痛点。

## 冷知识：Tailwind 比 Vue 火

虽说国内的前端开发者十有八九都会 Vue，且国内开发者普遍不喜欢 Tailwind，觉得 Tailwind 是行内样式秽土重生。但不得不承认，从 NPM 周下载量来看，Vue 和 Tailwind 不是一个数量级。（无意贬低 Vue，只是客观陈述 Tailwind 已经是主流技术选型。）

Tailwind CSS 为什么这么火？我觉得它解决了别的样式框架没解决的问题——如何共置。

想象一下，在用 Tailwind 之前我们在用什么？SCSS/Less/Stylus 这种预处理器。这些预处理器让 CSS 变得更加强大，这是毋庸置疑的，但使用它们，我们还是不得不来回切换我们的屏幕以看看样式的具体定义。

Tailwind CSS 解决了这个问题，我们只需要看一眼 class 的内容就可以看到样式是如何定义的，再也不用来回切屏幕，来回找定义了。

## 结语

以上就是我本次想做的分享，如果你能看到这里，我由衷地感谢。

我想给你一个建议：如果你只会 Vue（尤其是 Vue 2）、如果你没用过 Tailwind CSS，不关注这个领域技术的新动向，我的建议是赶紧学起来。我无意传播焦虑，这个行业本就如此，不进则退。

否则你也要跟着嚷"前端已死"了。
