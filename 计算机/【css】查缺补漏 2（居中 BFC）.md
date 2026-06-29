# CSS 查缺补漏 2：居中与 BFC

## 水平居中

### margin auto

```css
margin: 0 auto;
```

原理：当 margin 值为 auto 时，元素会尝试占据尽可能大的空间。`margin-left: auto` 和 `margin-right: auto` 同时作用，把盒子挤在水平居中位置。前提是元素有固定宽度。

### flex 布局

```css
display: flex;
justify-content: center;
```

## 垂直居中

### 绝对定位

```css
.parent {
  position: relative;
}
.child {
  width: 100px;
  height: 100px;
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -50px;
  margin-top: -50px;
  /* 或者 transform: translate(-50%, -50%); */
}
```

### flex 布局

```css
display: flex;
align-items: center;
```

`align-items` 控制交叉轴居中，配合 `justify-content: center` 实现水平垂直双居中。

## BFC（块级格式化上下文）

触发条件：
- `float` 不为 `none`
- `position: absolute` / `fixed`
- `display: inline-block` / `flex` / `grid` / `table-cell`
- `overflow` 不为 `visible`

作用：
- 清除浮动
- 防止 margin 折叠
- 自适应两栏布局
