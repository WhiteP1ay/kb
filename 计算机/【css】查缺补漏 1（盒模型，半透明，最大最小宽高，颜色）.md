# CSS 查缺补漏 1：盒模型、半透明、最大最小宽高、颜色

### box-sizing

默认值是 `content-box`，width/height 只算 content 区域，padding 和 border 向外叠加。

```css
div {
  width: 200px;
  padding: 20px;
  border: 5px solid black;
}
/* 实际占宽 = 200 + 20*2 + 5*2 = 250px */
```

写宽 200，实际元素宽度 250。布局容易算错。

`box-sizing: border-box`，width/height 算到 **border** 层，padding 和 border **往内挤**。

```css
div {
  box-sizing: border-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
}
/* content 实际宽度 = 200 - 20*2 - 5*2 = 150px */
```

因为 `border-box` 更符合直觉，几乎成为行业标准。几乎所有 CSS 框架（Bootstrap、Tailwind）和重置样式表都会写：

```css
*, *::before, *::after {
  box-sizing: border-box;
}
```

### 半透明

`opacity` 作用于整个元素（包括子元素），`rgba`/`hsla` 只作用于当前属性。

### max-width / min-width

`max-width` 优先级高于 `width`。当 `width` 和 `max-width` 同时存在时，实际宽度 = `min(width, max-width)`。

### 颜色表示

- 关键字：`red`, `blue` 等
- 十六进制：`#ff0000`、`#f00`
- RGB/RGBA：`rgb(255, 0, 0)`、`rgba(255, 0, 0, 0.5)`
- HSL/HSLA：`hsl(0, 100%, 50%)`
