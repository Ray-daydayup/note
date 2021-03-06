# 第四天的笔记

## 学习目标
1. 三种样式表的书写位置和优缺点
2. 标签的三种显示模式和转换
3. 复合选择器
4. 背景属性
5. css的三大特性

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

1. 可设置宽和高
2. 独占一行
3. 默认宽度是父级标签的宽度
4. 块级元素一般用于包裹其他块级或者行内元素
5. p这种段落标签不要包裹块级元素

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

### 行内块元素

1. 行内块标签其实本质上是一种特殊的行内标签 （text-align和line-height都可以控制行内块元素）
2. 允许其他的行内元素排一排
3. 可以设置宽高
/*如果将标签的高度和行高设置成一样不能使image垂直居中*/
```
代表标签：input,img
display:inline-block
```
### 标签之间的相互转换

```
/* 转换成行内元素 */
			display: inline;
/* 转换成块级元素 */
			display: block;
/* 转换成行内块级元素 */
            display: inline-block;
```
注：一般实际工作来说标签不需要进行转换，因为大多数情况下都可以选择对应的标签来实现需求 行内块较为特殊！！
## 复合选择器

1. 交集选择器  
```
既又原则 
例如：p.box {}  既是p标签 又有box类名
```
2. 并集选择器  
```
将多个选择器合并在一起 
例如： div,p,h1 {}  
```
3. **后代**选择器  
```
空格前面的元素和后面的元素必须是嵌套关系 （注意空格）只选择后面的后代元素
例如：.father .houdai {}
```
4. **子代**选择器  
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

### 测试题

```html
<div class="nav">    <!-- 主导航栏 -->
  <ul>
    <li><a href="#">公司首页</a></li>
	<li><a href="#">公司简介</a></li>
	<li><a href="#">公司产品</a></li>
	<li>
         <a href="#">联系我们</a>
		 <ul>
		    <li><a href="#">公司邮箱</a></li>
		    <li><a href="#">公司电话</a></li>
		 </ul>
	</li>
  </ul>
</div>
<div class="sitenav">    <!-- 侧导航栏 -->
  <div class="site-l">左侧侧导航栏</div>
  <div class="site-r"><a href="#">登录</a></div>
</div>
```

在不修改以上代码的前提下，完成以下任务：

1. 链接 登录 的颜色为红色,同时主导航栏里面的所有的链接改为蓝色     (简单)
2. 主导航栏和侧导航栏里面文字都是14像素并且是微软雅黑。（中等)
3. 主导航栏里面的一级菜单链接文字颜色为绿色。（难)

##  背景
### 背景颜色
```
background-color: red; 

颜色可以设置成十六进制 或者 rgb 或者 rgba 
```
### 背景图片
```
background-image: url(图片路径);
```
### 背景平铺
```
background-repeat: 背景平铺;

1.repeat 平铺  默认的
2.no-repeat 不平铺
3.repeat-x 水平平铺
4.repeat-y 垂直平铺
```
### 背景是否滚动
```
background-attachment: 背景是否滚动;

1.scroll  默认值 图片跟随盒子一起滚动
2.fixed	  图片不跟随盒子一起滚动
```
### 背景位置
```
background-position: 背景位置;

1.方位名词 right top left center bottom 书写的顺序不论
2.像素 如果是像素 那么默认在以“当前盒子”的左上角为0 0原点 构建一个坐标系 第一值是X轴的位置 第二个值是Y轴的位置 交互的点就是图片开始显示的起始位置
	多说一嘴：
	1、程序里面的坐标系X轴水平向右  Y轴垂直向下
	2、注意顺序
3.百分比  百分比参照的自身盒子的宽高： 最终的位置是当前盒子自身的宽高的百分比 - 图片自身的宽高的百分比
4.还可以混写 混写是需要考虑顺序
```
### 背景的简写
```
background:背景颜色 背景图片地址 背景平铺 背景滚动 背景位置;

如：background: #fff url() no-repeat scroll center center;
```

### img和背景图片的区别：
```
img不需要专门写宽高就能够显示在页面上
而背景图片默认是撑不开容器的 需要专门写宽高
一般产品插入图都推荐使用img  而一些小的icon 或者很少更新的图片 再或者超大的图片推荐使用背景图
而且背景图可以让内部的文字盖在上面，但是img不行（除非后期用定位）
```
## css的三大特性

### 继承性

> 后代元素会继承祖先元素的一些样式 跟文字相关属性可以继承 color  font-  line-  text- 可以继承  块元素可以继承父级元素的宽度，高度不继承

- 注意：a链接颜色不予继承，需要单独写
- 注意：如果元素自身有该css样式，那么该样式不予继承

```
<div class="father">
	爸爸
	<div class="son">
		儿子
		<div class="grandson">孙子</div>
	</div>
</div>
```

### 层叠性

> 后渲染的css样式会覆盖掉先渲染的css样式 （权重相同的情况下） 
> 注意：层叠性真针对css的书写位置，类的调用位置先后会它没有影响
> 后渲染的生效
> 层叠性：权重相同的情况下 后渲染的会覆盖掉先渲染的
> 调用类的位置对结果没有影响 声明类的位置才对结果有影响


```
<style>
	.box1 {
		/* 最终绿色起效果 */
		color: red;
		color: green;
	}
</style>

<div class="box"></div>
```

### 优先级（权重）

> 不同的选择器之间会有不同的优先级
```
继承 < 通配符 < 标签选择器 < 类选择器 < ID选择器 < 行内样式 <　!important
```

### 权重叠加

### CSS特殊性（Specificity）

关于CSS权重，我们需要一套计算公式来去计算，这个就是 CSS Specificity，我们称为CSS 特性或称非凡性，它是一个衡量CSS值优先级的一个标准 具体规范入如下：

specificity用一个四位的数 字串(CSS2是三位)来表示，更像四个级别，值从左到右，左面的最大，一级大于一级，数位之间没有进制，级别之间不可超越。 

| 继承或者* 的贡献值      | 0,0,0,0 |
| --------------- | ------- |
| 每个元素（标签）贡献值为    | 0,0,0,1 |
| 每个类，伪类贡献值为      | 0,0,1,0 |
| 每个ID贡献值为        | 0,1,0,0 |
| 每个行内样式贡献值       | 1,0,0,0 |
| 每个!important贡献值 |     正无穷    |

```
权重是可以叠加的

比如的例子：

div ul  li   ------>      0,0,0,3
.nav ul li   ------>      0,0,1,2
a:hover      -----—>      0,0,1,1
.nav a       ------>      0,0,1,1   
#nav p       ------>      0,1,0,1
```

