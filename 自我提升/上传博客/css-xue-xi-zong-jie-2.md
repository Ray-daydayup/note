# CSS学习总结（2）


## 样式表的书写位置

> 样式表可以有三种书写方式，分别分为

1. 内嵌式样式表
2. 外链式样式表
3. 行内式样式表

### 内嵌式样式表
> 内嵌式样式表是在html里面嵌套一个style标签，将css语句都写在style标签里面

```html
<style>
	css语句
</style>
```
### 外链式样式表
> 单独创建一个后缀名为.css的文件，在html文件里面通过link标签引入css文件

```html
<link rel="stylesheet" href="css文件的地址" />
```

**注意：  link 是个单标签**

该语法中，link标签需要放在head头部标签中，并且必须指定link标签的三个属性，具体如下：

~~~
href：定义所链接外部样式表文件的URL，可以是相对路径，也可以是绝对路径。
type：定义所链接文档的类型，在这里需要指定为“text/CSS”，表示链接的外部文件为CSS样式表。
rel：定义当前文档与被链接文档之间的关系，在这里需要指定为“stylesheet”，表示被链接的文档是一个样式表文件。
~~~

### 行内式样式表
> 将样式直接写在标签本身上，以属性的形式存在 

```html
<div style="color:green; font-size:20px;"></div>
```

### 三种样式表总结

|   样式表    |          优点          |          缺点          |   使用情况    |     控制范围      |
| ----------- | ---------------------- | ---------------------- | ------------- | ---------------- |
| 行内式样式表 | 书写方便，权重高        | 没有实现样式和结构相分离 | 较少          | 控制一个标签（少） |
| 内嵌式样式表 | 部分结构和样式相分离     | 没有彻底分离            | 较多          | 控制一个页面（中） |
| 外链式样式表 | 完全实现结构和样式相分离 | 需要引入                | 最多，强烈推荐 | 控制整个站点（多） |
内嵌式样式表和外链式样式表权重相同，和渲染先后有关 ，后渲染显示

## 标签的三种显示模式

### 块级元素

- 每个块元素通常都会独自占据一整行或多整行，可以对其设置宽度、高度、对齐等属性，常用于网页布局和网页结构的搭建。
1. 可设置宽和高
2. 独占一行
3. 默认宽度是父级标签的宽度
4. 块级元素一般用于包裹其他块级或者行内元素
5. `p`这种段落标签不要包裹块级元素

```
<!-- 块级标签：div p h1-h6 ol ul li dl dt dd header footer nav aside article section
		
		特点：
		1、可以直接写宽高
		2、独占一行
		3、默认宽度是父级标签的宽度
		4、一般用于作为容器布局 可以嵌套其他块级元素或者行内元素(p标签不要包裹其他块级元素)
代表标签：div,p,ol,li,ul,dt,dd,dl,header,footer,aside,nav,article,section
display:block;
```
### 行内元素

- 行内元素（内联元素）不占有独立的区域，仅仅靠自身的字体大小和图像尺寸来支撑结构，一般不可以设置宽度、高度、对齐等属性，常用于控制页面中文本的样式。
1. 设置宽高无效
2. 行内标签允许其他的行内标签排一排
3. 尽量不要用行内元素包裹块级元素 a链接除外

```
<!-- 行内元素：span a strong ins b del s u i em 
		
		特点：
		1、宽高对于行内元素没有作用
		2、可以允许其他行内元素排成一排
		3、内容有多大就撑多大 如果没有内容，那么没有大小
		4、一般用来包裹文字或者做一些小的装饰(行内标签不要嵌套块级标签 a除外)
	-->
代表标签：a,span,b,u,s,i,strong,ins,del,em
display:inline
```

**注意：**

1. 只有 文字才 能组成段落  因此 p  里面不能放块级元素，同理还有这些标签h1,h2,h3,h4,h5,h6,dt，他们都是文字类块级标签，里面不能放其他块级元素。
2. 链接里面不能再放链接。

### 行内块元素

1. 行内块标签其实本质上是一种特殊的行内标签 （text-align和line-height都可以控制行内块元素）
2. 允许其他的行内元素排一排
3. 可以设置宽高
/*如果将标签的高度和行高设置成一样不能使image垂直居中*/

```
代表标签：input,img
display:inline-block
```

### 块级元素和行内元素区别

~~~
块级元素的特点：
（1）总是从新行开始
（2）高度，行高、外边距以及内边距都可以控制。
（3）宽度默认是容器的100%
（4）可以容纳内联元素和其他块元素。
~~~

~~~
行内元素的特点：
（1）和相邻行内元素在一行上。
（2）高、宽无效，但水平方向的padding和margin可以设置，垂直方向的无效。
（3）默认宽度就是它本身内容的宽度。
（4）行内元素只能容纳文本或则其他行内元素。
~~~

### 标签之间的相互转换

```css
/* 转换成行内元素 */
			display: inline;
/* 转换成块级元素 */
			display: block;
/* 转换成行内块级元素 */
            display: inline-block;
```
注：一般实际工作来说标签不需要进行转换，因为大多数情况下都可以选择对应的标签来实现需求 行内块较为特殊！！

## 复合选择器

### 交集选择器  
```
既又原则 
例如：p.box {}  既是p标签 又有box类名
```
### 并集选择器  
```
将多个选择器合并在一起 
例如： div,p,h1 {}  
```
### 后代选择器  
```
空格前面的元素和后面的元素必须是嵌套关系 （注意空格）只选择后面的后代元素
例如：.father .houdai {}
```
### 子代选择器  
```
> 前面的元素和后面的元素必须是父子关系 
例如：.father > .son {}
```
* **注意区分后代和子代的区别**

```
        /* 后代选择器  
			选择器1 空格 选择器2  
			两个选择器满足后代关系
		*/

		/* 子代选择器 
		选择器1 > 选择器2

		两个元素之间必须满足子父级关系
			

		*/
```

### 属性选择器

选取标签带有某些特殊属性的选择器 我们成为属性选择器

~~~css
/* 获取到 拥有 该属性的元素 */
div[class^=font] { /*  class^=font 表示 font 开始位置就行了 */
			color: pink;
		}
div[class$=footer] { /*  class$=footer 表示 footer 结束位置就行了 */
			color: skyblue;
		}
div[class*=tao] { /* class*=tao  *=  表示tao 在任意位置都可以 */
			color: green;
		}
~~~

~~~html
<div class="font12">属性选择器</div>
    <div class="font12">属性选择器</div>
    <div class="font24">属性选择器</div>
    <div class="font24">属性选择器</div>
    <div class="font24">属性选择器</div>
    <div class="24font">属性选择器123</div>
    <div class="sub-footer">属性选择器footer</div>
    <div class="jd-footer">属性选择器footer</div>
    <div class="news-tao-nav">属性选择器</div>
    <div class="news-tao-header">属性选择器</div>
    <div class="tao-header">属性选择器</div>
~~~

### 伪元素选择器（CSS3)

1. E::first-letter文本的第一个单词或字（如中文、日文、韩文等）
2. E::first-line 文本第一行；
3. E::selection 可改变选中文本的样式；

~~~css
p::first-letter {
  font-size: 20px;
  color: hotpink;
}

/* 首行特殊样式 */
p::first-line {
  color: skyblue;
}

p::selection {
  /* font-size: 50px; */
  color: orange;
}
~~~



4、E::before和E::after

在E元素内部的开始位置和结束位创建一个元素，该元素为行内元素，且必须要结合content属性使用。

~~~css
div::befor {
  content:"开始";
}
div::after {
  content:"结束";
}
~~~



E:after、E:before 在旧版本里是伪元素，CSS3的规范里“:”用来表示伪类，“::”用来表示伪元素，但是在高版本浏览器下E:after、E:before会被自动识别为E::after、E::before，这样做的目的是用来做兼容处理。

E:after、E:before后面的练习中会反复用到，目前只需要有个大致了解

":" 与 "::" 区别在于区分伪类和伪元素

## CSS书写规范

开始就形成良好的书写规范，是你专业化的开始。

### 空格规范

【强制】 选择器 与 { 之间必须包含空格。

示例： .selector { }

【强制】 属性名 与之后的 : 之间不允许包含空格， : 与 属性值 之间必须包含空格。

示例：

font-size: 12px;

### 选择器规范

【强制】 当一个 rule 包含多个 selector 时，每个选择器声明必须独占一行。

示例：

```
/* good */
.post,
.page,
.comment {
    line-height: 1.5;
}


/* bad */
.post, .page, .comment {
    line-height: 1.5;
}
```

【建议】 选择器的嵌套层级应不大于 3 级，位置靠后的限定条件应尽可能精确。

示例：

```css
/* good */
#username input {}
.comment .avatar {}

/* bad */
.page .header .login #username input {}
.comment div * {}
```

### 属性规范

【强制】 属性定义必须另起一行。

示例：

```css
/* good */
.selector {
    margin: 0;
    padding: 0;
}

/* bad */
.selector { margin: 0; padding: 0; }
```

【强制】 属性定义后必须以分号结尾。

示例：

```css
/* good */
.selector {
    margin: 0;
}

/* bad */
.selector {
    margin: 0
}
```