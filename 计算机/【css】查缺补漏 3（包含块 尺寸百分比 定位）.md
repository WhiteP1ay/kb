# CSS 查缺补漏 3：包含块、尺寸百分比、定位

## 五种定位

- **static**：标准文档流。`left`/`right`/`top`/`bottom` 不生效。
- **relative**：基于标准文档流偏移，通常用来微调位置或给绝对定位提供参考系。
- **absolute**：基于包含块（**非 static 的祖先元素的 content + padding**），会脱离文档流，元素 display 会变 block。如果父级元素没有定位元素，包含块是 **initial containing block**（即 html 元素的 content 区，尺寸等于视口，原点在文档左上角）。如果此时设定 `bottom`，是基于**首屏窗口**计算下间距。
- **fixed**：脱离文档流，基于 viewport（视口）。
- **sticky**：参考系是**最近的滚动祖先（scrolling ancestor）**。如果没有任何滚动祖先，则是 viewport。需要指定阈值。

### fixed 的坑

**transform/filter/perspective ≠ none 的祖先元素，会成为 fixed 的包含块，导致 fixed 不再相对于视口。**

## 尺寸百分比

`width`/`height` 百分比相对于**包含块**的 content 区。`padding`/`margin` 百分比（包括上下方向）相对于包含块的 **width**。
