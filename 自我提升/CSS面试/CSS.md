### 盒模型

![CSS Box model](_v_images/boxmodel-(3).png)

- **内容区域 content area** ，由内容边界限制，容纳着元素的“真实”内容，例如文本、图像，或是一个视频播放器。它的尺寸为内容宽度（或称 *content-box 宽度*）和内容高度（或称 *content-box 高度*）。它通常含有一个背景颜色（默认颜色为透明）或背景图像。如果 [`box-sizing`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing) 为 `content-box`（默认），则内容区域的大小可明确地通过 [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width)、[`min-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-width)、[`max-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-width)、[`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height)、[`min-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-height)，和 [`max-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-height) 控制。
- **内边距区域 padding area** 由内边距边界限制，扩展自内容区域，负责延伸内容区域的背景，填充元素中内容与边框的间距。它的尺寸是 *padding-box 宽度* 和 *padding-box 高度*。内边距的粗细可以由 [`padding-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding-top)、[`padding-right`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding-right)、[`padding-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding-bottom)、[`padding-left`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding-left)，和简写属性 [`padding`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding) 控制。
- **边框区域 border area** 由边框边界限制，扩展自内边距区域，是容纳边框的区域。其尺寸为 *border-box 宽度* 和 *border-box 高度*。边框的粗细由 [`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width) 和简写的 [`border`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border) 属性控制。如果 [`box-sizing`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing) 属性被设为 `border-box`，那么边框区域的大小可明确地通过 [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width)、[`min-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-width), [`max-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-width)、[`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height)、[`min-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-height)，和 [`max-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-height) 属性控制。假如框盒上设有背景（[`background-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-color) 或 [`background-image`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-image)），背景将会一直延伸至边框的外沿（默认为在边框下层延伸，边框会盖在背景上）。此默认表现可通过 CSS 属性 [`background-clip`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-clip) 来改变。

- **外边距区域 margin area** 由外边距边界限制，用空白区域扩展边框区域，以分开相邻的元素。它的尺寸为 *margin-box 宽度* 和 *margin-box 高度*。外边距区域的大小由 [`margin-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-top)、[`margin-right`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-right)、[`margin-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-bottom)、[`margin-left`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-left)，和简写属性 [`margin`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin) 控制。在发生[外边距合并](https://developer.mozilla.org/en/CSS/margin_collapsing)的情况下，由于盒之间共享外边距，外边距不容易弄清楚。

### 外边距重叠

块的[上外边距(margin-top)](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-top)和[下外边距(margin-bottom)](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-bottom)有时合并(折叠)为单个边距，**其大小为单个边距的最大值(或如果它们相等，则仅为其中一个)**，这种行为称为**边距折叠**。

- **同一层相邻元素之间**  相邻的两个元素之间的外边距重叠，除非后一个元素加上[clear-fix清除浮动](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clear)。

```html
<style>   
p:nth-child(1){   
  margin-bottom: 13px; 
}   
p:nth-child(2){  
  margin-top: 87px;  
} 
</style>
 
<p>下边界范围会...</p>
<p>...会跟这个元素的上边界范围重叠。</p>
```

- **没有内容将父元素和后代元素分开**   如果**没有边框`border`，内边距`padding`，行内内容，也没有创建`块级格式上下文`或`清除浮动`来分开一个块级元素的上边界[`margin-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-top) 与其内一个或多个后代块级元素的上边界[`margin-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-top)**；**或没有边框，内边距，行内内容，高度[`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height)，最小高度[`min-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-height)或 最大高度[`max-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-height) 来分开一个块级元素的下边界[`margin-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-bottom)与其内的一个或多个后代后代块元素的下边界[`margin-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-bottom)**，则就会出现父块元素和其内后代块元素外边界重叠，重叠部分最终会溢出到父级块元素外面。

```html
<style type="text/css">
    section    {
        margin-top: 13px;
        margin-bottom: 87px;
    }

    header {
        margin-top: 87px;
    }

    footer {
        margin-bottom: 13px;
    }
</style>

<section>
    <header>上边界重叠 87</header>
    <main></main>
    <footer>下边界重叠 87 不能再高了</footer>
</section>
```

- **空的块级元素**  当一个块元素上边界[`margin-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-top) 直接贴到元素下边界[`margin-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-bottom)时也会发生边界折叠。这种情况会发生在一个块元素完全没有设定边框[`border`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border)、内边距[`paddng`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/paddng)、高度[`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height)、最小高度[`min-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-height) 、最大高度[`max-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-height) 、内容设定为inline或是加上[clear-fix](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clear)的时候。

```html
<style>
p {
  margin: 0;  
}
div {
  margin-top: 13px;
  margin-bottom: 87px;
}
</style>

<p>上边界范围是 87 ...</p>
<div></div>
<p>... 上边界范围是 87</p>
```

### BFC 块级格式上下文

**块格式化上下文（Block Formatting Context，BFC）** 是Web页面的可视CSS渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

1. BFC有隔离作用，内部元素不会被外部元素影响（反之亦然）
2. 一个元素只存在一个BFC之中
3. BFC按照正常流排列
4. BFC不会与浮动元素重叠
5. 计算BFC的高度，包括其浮动子元素。(解决浮动撑不起来父盒子)

**创建方法**：

- 根元素（`<html>）`
- 浮动元素（元素的 [`float`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float) 不是 `none`）
- 绝对定位元素（元素的 [`position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position) 为 `absolute` 或 `fixed`）
- 行内块元素（元素的 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `inline-block`）
- 表格单元格（元素的 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `table-cell`，HTML表格单元格默认为该值）
- 表格标题（元素的 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `table-caption`，HTML表格标题默认为该值）
- 匿名表格单元格元素（元素的 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `table、``table-row`、 `table-row-group、``table-header-group、``table-footer-group`（分别是HTML table、row、tbody、thead、tfoot 的默认属性）或 `inline-table`）
- [`overflow`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow) 值不为 `visible` 的块元素
- [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 值为 `flow-root` 的元素
- [`contain`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/contain) 值为 `layout`、`content `或 paint 的元素
- 弹性元素（[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `flex` 或 `inline-flex `元素的直接子元素）
- 网格元素（[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `grid` 或 `inline-grid` 元素的直接子元素）
- 多列容器（元素的 [`column-count`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/column-count) 或 [`column-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/column-width) 不为 `auto，包括 ``column-count` 为 `1`）
- `column-span` 为 `all` 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中（[标准变更](https://github.com/w3c/csswg-drafts/commit/a8634b96900279916bd6c505fda88dda71d8ec51)，[Chrome bug](https://bugs.chromium.org/p/chromium/issues/detail?id=709362)）。

**用途**：

1. 解决浮动
2. 解决外边距塌陷
3. 宽度自适应两栏布局

### 层叠上下文 z-index

某些元素的渲染顺序是由其 `z-index` 的值影响的。这是因为这些元素具有能够使他们形成一个*层叠上下文*的特殊属性。属性必须是整数(正负均可)， 它体现了元素在z轴的位置。 如果你对z轴体系不了解， 你也可以把它理解成“层叠”， 每个层都有一个顺序数， 顺序数大的层在上面， 小的在下面。 

- 当没有指定z-index的时候， 所有元素都在会被渲染在默认层(0层)
- 当多个元素的z-index属性相同的时候(在同一个层里面)，那么将按照 [Stacking without z-index](https://developer.mozilla.org/en/CSS/Understanding_z-index/Stacking_without_z-index) 中描述的规则进行布局。

文档中的层叠上下文由满足以下任意一个条件的元素形成：

- 文档根元素（`<html>`）；
- [`position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position) 值为 `absolute`（绝对定位）或 `relative`（相对定位）且 [`z-index`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/z-index) 值不为 `auto` 的元素；
- [`position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position) 值为 `fixed`（固定定位）或 `sticky`（粘滞定位）的元素（沾滞定位适配所有移动设备上的浏览器，但老的桌面浏览器不支持）；
- flex ([`flexbox`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flexbox)) 容器的子元素，且 [`z-index`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/z-index) 值不为 `auto`；
- grid ([`grid`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/grid)) 容器的子元素，且 [`z-index`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/z-index) 值不为 `auto`；
- [`opacity`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/opacity) 属性值小于 `1` 的元素（参见 [the specification for opacity](http://www.w3.org/TR/css3-color/#transparency)）；
- [`mix-blend-mode`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/mix-blend-mode) 属性值不为 `normal` 的元素；
- 以下任意属性值不为`none`的元素：
  - [`transform`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform)
  - [`filter`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter)
  - [`perspective`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/perspective)
  - [`clip-path`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clip-path)
  - [`mask`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/mask) / [`mask-image`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/mask-image) / [`mask-border`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/mask-border)
- [`isolation`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/isolation) 属性值为 `isolate` 的元素；
- [`-webkit-overflow-scrolling`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/-webkit-overflow-scrolling) 属性值为 `touch` 的元素；
- [`will-change`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/will-change) 值设定了任一属性而该属性在 non-initial 值时会创建层叠上下文的元素（参考[这篇文章](http://dev.opera.com/articles/css-will-change-property/)）；
- [`contain`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/contain) 属性值为 `layout`、`paint` 或包含它们其中之一的合成值（比如 `contain: strict`、`contain: content`）的元素。

在层叠上下文中，子元素同样也按照上面解释的规则进行层叠。 重要的是，其子级层叠上下文的 `z-index` 值只在父级中才有意义。子级层叠上下文被自动视为父级层叠上下文的一个独立单元。

总结:

- 层叠上下文可以包含在其他层叠上下文中，并且一起创建一个层叠上下文的层级。
- 每个层叠上下文都完全独立于它的兄弟元素：当处理层叠时只考虑子元素。
- 每个层叠上下文都是自包含的：当一个元素的内容发生层叠后，该元素将被**作为整体在父级层叠上下文中按顺序进行层叠**（父元素的z-index小于其他同级，他子元素在下面）

### 包含块

一个元素的尺寸和位置经常受其**包含块(containing block)**的影响。大多数情况下，包含块就是这个元素最近的祖先[块元素](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Block-level_elements)的[内容区](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model#content-area)，但也不是总是这样。在本文中，我们来过一遍确定包含块的所有因素。

确定一个元素的包含块的过程完全依赖于这个元素的 [`position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position) 属性：

1. 如果 position 属性为 `**static**` 、 `**relative**` **或 `sticky`**，包含块可能由它的最近的祖先**块元素**（比如说inline-block, block 或 list-item元素）的内容区的边缘组成，也可能会建立格式化上下文(比如说 a table container, flex container, grid container, 或者是 the block container 自身)。
2. 如果 position 属性为 `**absolute**` ，包含块就是由它的最近的 position 的值不是 `static` （也就是值为`fixed`, `absolute`, `relative` 或 `sticky`）的祖先元素的内边距区的边缘组成。
3. 如果 position 属性是 **`fixed`**，在连续媒体的情况下(continuous media)包含块是 [viewport](https://developer.mozilla.org/zh-CN/docs/Glossary/Viewport) ,在分页媒体(paged media)下的情况下包含块是分页区域(page area)。（**当前视口**）
4. 如果 position 属性是absolute或fixed，包含块也可能是由满足以下条件的最近父级元素的内边距区的边缘组成的：
   1. A [`transform`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform) or [`perspective`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/perspective) value other than `none`
   2. A [`will-change`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/will-change) value of `transform` or `perspective`
   3. A [`filter`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter) value other than `none` or a `will-change` value of `filter`(only works on Firefox).
   4. A [`contain`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/contain) value of `paint` (例如: `contain: paint;`)

**根据包含块计算百分值**

如上所述，如果某些属性被赋予一个百分值的话，它的计算值是由这个元素的包含块计算而来的。这些属性包括盒模型属性和偏移属性：

1. 要计算 [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height) [`top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/top) 及 [`bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/bottom) 中的百分值，是通过包含块的 `height` 的值。如果包含块的 `height` 值会根据它的内容变化，而且包含块的 `position` 属性的值被赋予 `relative` 或 `static` ，那么，这些值的计算值为 auto。
2. 要计算 [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width), [`left`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/left), [`right`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/right), [`padding`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding), [`margin`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin) 这些属性由包含块的 `width` 属性的值来计算它的百分值。

### 优先级

- ！important标记为重要的

- **级联**来源：标记为重要的用户样式>标记为重要的作者样式（内联、内嵌、外部样式）>内联样式>内嵌样式>外部样式>用户样式>用户代理样式
- 特殊性（选择器）：ID》类》标签    **通配选择符**（universal selector）（[`*`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Universal_selectors)）**关系选择符**（combinators）（[`+`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Adjacent_sibling_combinator), [`>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Child_combinator), [`~`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/General_sibling_combinator), ['` `'](https://developer.mozilla.org/en-US/docs/Web/CSS/Descendant_combinator), [`||`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Column_combinator)）和 **否定伪类**（negation pseudo-class）（[`:not()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:not)）对优先级没有影响
- **继承**：默认元素外观相关的属性，但是布局相关不会。如果想继承非继承属性，显式用inherit

### 浮动