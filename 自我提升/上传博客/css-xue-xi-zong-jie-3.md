# css学习总结（3）


#  背景

CSS 可以添加背景颜色和背景图片，以及来进行图片设置。

|                    background-color                     |    背景颜色     |
| ------------------------------------------------------- | --------------- |
| background-image                                        | 背景图片地址     |
| background-repeat                                       | 是否平铺        |
| background-position                                     | 背景位置        |
| background-attachment                                   | 背景固定还是滚动 |
| 背景的合写（复合属性）                                    |                 |
| background:背景颜色 背景图片地址 背景平铺 背景滚动 背景位置 |                 |

## 背景颜色
```
background-color: red; 

颜色可以设置成十六进制 或者 rgb 或者 rgba 
```
## 背景图片
```
background-image: url(图片路径);
```
## 背景平铺
```
background-repeat: 背景平铺;

1.repeat 平铺  默认的
2.no-repeat 不平铺
3.repeat-x 水平平铺
4.repeat-y 垂直平铺
```
## 背景是否滚动
```
background-attachment: 背景是否滚动;

1.scroll  默认值 图片跟随盒子一起滚动
2.fixed	  图片不跟随盒子一起滚动
```
## 背景位置
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
## 背景的简写
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

## 背景透明(CSS3)

CSS3支持背景半透明的写法语法格式是:

~~~css
background: rgba(0,0,0,0.3);
~~~

 最后一个参数是alpha 透明度  取值范围 0~1之间

 注意：  背景半透明是指盒子背景半透明， 盒子里面的内容不收影响。


## 背景缩放(CSS3)

通过background-size设置背景图片的尺寸，就像我们设置img的尺寸一样，在移动Web开发中做屏幕适配应用非常广泛。
其参数设置如下：

1. 可以设置长度单位(px)或百分比（设置百分比时，参照盒子的宽高）
2. 设置为cover时，会自动调整缩放比例，保证图片始终填充满背景区域，如有溢出部分则会被隐藏。我们平时用的cover 最多
3. 设置为contain会自动调整缩放比例，保证图片始终完整显示在背景区域。

~~~css
background-image: url('images/gyt.jpg');
			background-size: 300px 100px;
			/* background-size: contain; */
			/* background-size: cover; */
~~~

## 多背景(CSS3)

以逗号分隔可以设置多背景，可用于自适应布局  做法就是 用逗号隔开就好了。

- 一个元素可以设置多重背景图像。 
- 每组属性间使用逗号分隔。 
- 如果设置的多重背景图之间存在着交集（即存在着重叠关系），前面的背景图会覆盖在后面的背景图之上。
- 为了避免背景色将图像盖住，背景色通常都定义在最后一组上，

~~~css
background:url(test1.jpg) no-repeat scroll 10px 20px/50px 60px  ,
	   url(test1.jpg) no-repeat scroll 10px 20px/70px 90px ,
	   url(test1.jpg) no-repeat scroll 10px 20px/110px 130px c #aaa;
~~~

## 凹凸文字

~~~css
<head>
        <meta charset="utf-8">
        <style>
        body {
        	background-color: #ccc;
        }
		div {
			color: #ccc;
			font: 700 80px "微软雅黑";
		}
		div:first-child {
			/* text-shadow: 水平位置  垂直位置  模糊距离 阴影颜色; */
			text-shadow: 1px 1px 1px #000, -1px -1px 1px #fff;
		}
		div:last-child {
			/* text-shadow: 水平位置  垂直位置  模糊距离 阴影颜色; */
			text-shadow: -1px -1px 1px #000, 1px 1px 1px #fff;
		}

        </style>
    </head>
    <body>
    <div>我是凸起的文字</div>
    <div>我是凹下的文字</div>
    </body>
~~~

# 文本的装饰

text-decoration   通常我们用于给链接修改装饰效果

| 值            | 描述                      |
| ------------ | ----------------------- |
| none         | 默认。定义标准的文本。             |
| underline    | 定义文本下的一条线。下划线 也是我们链接自带的 |
| overline     | 定义文本上的一条线。              |
| line-through | 定义穿过文本下的一条线。            |
|              |                         |
|              |                         |

**使用技巧**：在一行内的盒子内，我们设定行高等于盒子的高度，就可以使文字垂直居中。

# css的三大特性

## 继承性

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

## 层叠性

> 后渲染的css样式会覆盖掉先渲染的css样式 （权重相同的情况下） 
> 注意：层叠性真针对css的书写位置，类的调用位置先后会它没有影响
> 后渲染的生效
> 层叠性：权重相同的情况下 后渲染的会覆盖掉先渲染的
> 调用类的位置对结果没有影响 声明类的位置才对结果有影响


```css
<style>
	.box1 {
		/* 最终绿色起效果 */
		color: red;
		color: green;
	}
</style>

<div class="box"></div>
```

## 优先级（权重）

> 不同的选择器之间会有不同的优先级

```
继承 < * < 标签选择器 < 类选择器 < id选择器 < 行内样式 < !important 
	!important 只能作用单条属性  并不是整个选择器（继承不起作用）
```

定义CSS样式时，经常出现两个或更多规则应用在同一元素上，这时就会出现优先级的问题。

在考虑权重时，需要注意一些特殊的情况，具体如下：

~~~
继承样式的权重为0。即在嵌套结构中，不管父元素样式的权重多大，被子元素继承时，他的权重都为0，也就是说子元素定义的样式会覆盖继承来的样式。

行内样式优先。应用style属性的元素，其行内样式的权重非常高，可以理解为远大于100。总之，他拥有比上面提高的选择器都大的优先级。

权重相同时，CSS遵循就近原则。也就是说靠近元素的样式具有最大的优先级，或者说排在最后的样式优先级最大。

CSS定义了一个!important命令，该命令被赋予最大的优先级。也就是说不管权重如何以及样式位置的远近，!important都具有最大优先级。
~~~

## 权重叠加

## CSS特殊性（Specificity）

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

**注意：**

1. 数位之间没有进制 比如说： 0,0,0,5 + 0,0,0,5 =0,0,0,10 而不是 0,0, 1, 0， 所以不会存在10个div能赶上一个类选择器的情况。
2. 继承的 权重是 0

**总结优先级：**

1. 使用了 !important声明的规则。
2. 内嵌在 HTML 元素的 style属性里面的声明。
3. 使用了 ID 选择器的规则。
4. 使用了类选择器、属性选择器、伪元素和伪类选择器的规则。
5. 使用了元素选择器的规则。
6. 只包含一个通用选择器的规则。
7. 同一类选择器则遵循就近原则。

~~~
总结：权重是优先级的算法，层叠是优先级的表现
~~~


# 盒子模型（CSS重点）

所谓盒子模型就是把HTML页面中的元素看作是一个矩形的盒子，也就是一个盛装内容的容器。每个矩形都由元素的内容、内边距（padding）、边框（border）和外边距（margin）组成。

## 盒子模型（Box Model）


所有的文档元素（标签）都会生成一个矩形框，我们成为元素框（element box），它描述了一个文档元素再网页布局汇总所占的位置大小。因此，<strong style="color: #f00;">每个盒子除了有自己大小和位置外，还影响着其他盒子的大小和位置。</strong>


## 盒子边框（border）

边框就是那层皮。  橘子皮。。柚子皮。。橙子皮。。。

语法： 

~~~css
border : border-width || border-style || border-color 
~~~

边框属性—设置边框样式（border-style）

**边框样式用于定义页面中边框的风格，常用属性值如下：**
~~~
none：没有边框即忽略所有边框的宽度（默认值）
solid：边框为单实线(最为常用的)
dashed：边框为虚线  
dotted：边框为点线
double：边框为双实线
~~~

### 盒子边框写法总结表

|        |                                          |                                          |
| ------ | ---------------------------------------- | ---------------------------------------- |
| 设置内容   | 样式属性                                     | 常用属性值                                    |
| 上边框    | border-top-style:样式; border-top-width:宽度;border-top-color:颜色;border-top:宽度 样式 颜色; |                                          |
| 下边框    | border-bottom-style:样式;border- bottom-width:宽度;border- bottom-color:颜色;border-bottom:宽度 样式 颜色; |                                          |
| 左边框    | border-left-style:样式; border-left-width:宽度;border-left-color:颜色;border-left:宽度 样式 颜色; |                                          |
| 右边框    | border-right-style:样式;border-right-width:宽度;border-right-color:颜色;border-right:宽度 样式 颜色; |                                          |
| 样式综合设置 | border-style:上边 [右边 下边 左边];              | none无（默认）、solid单实线、dashed虚线、dotted点线、double双实线 |
| 宽度综合设置 | border-width:上边 [右边 下边 左边];              | 像素值                                      |
| 颜色综合设置 | border-color:上边 [右边 下边 左边];              | 颜色值、#十六进制、rgb(r,g,b)、rgb(r%,g%,b%)       |
| 边框综合设置 | border:四边宽度 四边样式 四边颜色;                   |                                          |

+ 补充：细线表格 border-collapse:collapse;
+ 圆角：borde-radius:值; 一个值控制的上左 上右 下右 下左;(CSS3)

```
			边框圆角语法：border-radius:像素;
			取值还可以是百分比 百分比参照的是当前盒子完整的宽高
			注意：边框圆角并不需要依赖与边框
			
			 border:10px solid #ccc; 
			 border-radius: 20px; 
			 border-radius: 50%; 	
			 
			单独控制圆角 
			一个值：四个角
			两个值：对角线
			三个值：左上  对角线  右下
			四个值：从左上开始 顺时针
			
```



## 内边距（padding）

padding属性用于设置内边距。  是指 边框与内容之间的距离。
padding-top:上内边距
padding-right:右内边距
padding-bottom:下内边距
padding-left:左内边距

注意：  后面跟几个数值表示的意思是不一样的。

| 值的个数 |                                                      表达意思                                                      |
| ------- | ----------------------------------------------------------------------------------------------------------------- |
| 1个值    | padding：上下左右边距 比如padding: 3px; 表示上下左右都是3像素                                                        |
| 2个值    | padding: 上下边距 左右边距 比如 padding: 3px 5px; 表示 上下3像素 左右 5像素                                           |
| 3个值    | padding：上边距 左右边距 下边距 比如 padding: 3px 5px 10px; 表示 上是3像素 左右是5像素 下是10像素                      |
| 4个值    | padding:上内边距 右内边距 下内边距 左内边距 比如: padding: 3px 5px 10px 15px; 表示 上3px 右是5px 下 10px 左15px 顺时针 |

## 外边距（margin）

margin属性用于设置外边距。  设置外边距会在元素之间创建“空白”， 这段空白通常不能放置其他内容。
margin-top:上外边距
margin-right:右外边距
margin-bottom:下外边距
margin-left:上外边距
margin:上外边距 右外边距  下外边距  左外边

取值顺序跟内边距相同。

**注意**：

1. 行内元素里面不要写上下padding，左右可以 
2. 行内元素不要写上下的padding值  左右的可以写 一般控制文字的上下 用行高即可
3. 块元素宽度的继承继承的是width的大小，不继承padding

### 外边距实现盒子居中

可以让一个盒子实现水平居中，需要满足一下两个条件：

1. 必须是块级元素。     
2. 盒子必须指定了宽度（width）

然后就给**左右的外边距都设置为auto**，就可使块级元素水平居中。


~~~css
.header{ width:960px; margin:0 auto;}
~~~

### 文字盒子居中图片和背景区别

1.  文字水平居中是  text-align: center
2.  盒子水平居中  左右margin 改为 auto 

~~~css
text-align: center; /*  文字居中水平 */
margin: 10px auto;  /* 盒子水平居中  左右margin 改为 auto 就阔以了 */
~~~

3. 插入图片 我们用的最多 比如产品展示类
4. 背景图片我们一般用于小图标背景 或者 超大背景图片

~~~css
section img {  
		width: 200px;/* 插入图片更改大小 width 和 height */
		height: 210px;
		margin-top: 30px;  /* 插入图片更改位置 可以用margin 或padding  盒模型 */
		margin-left: 50px; /* 插入当图片也是一个盒子 */
	}

aside {
		width: 400px;
		height: 400px;
		border: 1px solid purple;
		background: #fff url(images/sun.jpg) no-repeat;
	
		background-size: 200px 210px; /*  背景图片更改大小只能用 background-size */
		background-position: 30px 50px; /* 背景图片更该位置 我用 background-position */
	}
~~~



### 清除元素的默认内外边距

~~~css
* {
   padding:0;         /* 清除内边距 */
   margin:0;          /* 清除外边距 */
}
~~~
**注意：**  **行内元素是只有左右外边距的，是没有上下外边距的**。 内边距，在ie6等低版本浏览器也会有问题。

## 外边距的BUG

> 相邻块元素垂直外边距的合并

```
当上下相邻的两个块元素相遇时，如果上面的元素有下外边距margin-bottom，
下面的元素有上外边距margin-top，则他们之间的垂直间距不是margin-bottom与margin-top之和
而是两者中的较大者。这种现象被称为相邻块元素垂直外边距的合并（也称外边距塌陷）。
```
![](_v_images/20200313150435762_2684.png)

解决方案：  避免就好了。

> 嵌套块元素垂直外边距的合并

```
对于两个嵌套关系的块元素，如果父元素没有上内边距及边框，
则父元素的上外边距会与子元素的上外边距发生合并，
合并后的外边距为两者中的较大者，
即使父元素的上外边距为0，也会发生合并。
```
![](_v_images/20200313150525280_23114.png)


解决方案：

```
1. 可以为父元素定义1像素的上边框或上内边距。
2. 可以为父元素添加overflow:hidden。
```

1. **实际应用注意：在实际工作中，我们很难直接话的得到盒子的内容的大小，所以我们会直接将整个盒子量出来，在后续如果需要添加padding的情况下 一定要减掉 padding **

2. 如果这个块级盒子没有width属性（从父级继承宽度）的时候，添加padding和border不会撑大盒子（盒子内容部分会自动压缩）


## content宽度和高度

使用宽度属性width和高度属性height可以对盒子的大小进行控制。
width和height的属性值可以为不同单位的数值或相对于父元素的百分比%，实际工作中最常用的是像素值。

大多数浏览器，如Firefox、IE6及以上版本都采用了W3C规范，符合CSS规范的盒子模型的总宽度和总高度的计算原则是：

```
  /*外盒尺寸计算（元素空间尺寸）*/
  Element空间高度 = content height + padding + border + margin
  Element 空间宽度 = content width + padding + border + margin
  /*内盒尺寸计算（元素实际大小）*/
  Element Height = content height + padding + border （Height为内容高度）
  Element Width = content width + padding + border （Width为内容宽度）
```

**注意：**

1. 宽度属性width和高度属性height仅适用于块级元素，对行内元素无效（ img 标签和 input除外）。

2. 计算盒子模型的总高度时，还应考虑上下两个盒子垂直外边距合并的情况。

3. **如果一个盒子没有给定宽度/高度或者继承父亲的宽度/高度，则padding 不会影响本盒子大小**。

## 盒子模型布局稳定性


按照 优先使用  宽度 （width）  其次 使用内边距（padding）    再次  外边距（margin）。   

```
  width >  padding  >   margin   
```

原因：

1. margin 会有外边距合并 还有 ie6下面margin 加倍的bug（讨厌）所以最后使用。

2. padding  会影响盒子大小， 需要进行加减计算（麻烦） 其次使用。

3. width   没有问题（嗨皮）我们经常使用宽度剩余法 高度剩余法来做。

   ​
## CSS3盒模型

CSS3中可以通过box-sizing 来指定盒模型，即可指定为content-box、border-box，这样我们计算盒子大小的方式就发生了改变。

可以分成两种情况：

1、box-sizing: content-box  盒子大小为 width + padding + border   content-box:此值为其默认值，其让元素维持W3C的标准Box Mode

2、box-sizing: border-box  盒子大小为 width    就是说  padding 和 border 是包含到width里面的

注：上面的标注的width指的是CSS属性里设置的width: length，content的值是会自动调整的。

~~~css
div:first-child {
			width: 200px;
			height: 200px;
			background-color: pink; 
			box-sizing: content-box;  /*  就是以前的标准盒模型  w3c */
			padding: 10px;
			border: 15px solid red;
			/* 盒子大小为 width + padding + border   content-box:此值为其默认值，其让元素维持W3C的标准Box Mode */
		}
		div:last-child {
			width: 200px;
			height: 200px;
			background-color: purple;
			padding: 10px;
			box-sizing: border-box;   /* padding border  不撑开盒子 */
			border: 15px solid red;
			/* margin: 10px; */
			/* 盒子大小为 width    就是说  padding 和 border 是包含到width里面的 */
}
~~~

## 盒子阴影

语法格式：

~~~css
box-shadow:水平阴影 垂直阴影 模糊距离 阴影尺寸 阴影颜色  内/外阴影；
~~~

![1498467567011](media/1498467567011.png)

1. 前两个属性是必须写的。其余的可以省略。
2. 外阴影 (outset) 但是不能写    默认      想要内阴影  inset 

~~~css
div {
			width: 200px;
			height: 200px;
			border: 10px solid red;
			/* box-shadow: 5px 5px 3px 4px rgba(0, 0, 0, .4);  */
			/* box-shadow:水平位置 垂直位置 模糊距离 阴影尺寸（影子大小） 阴影颜色  内/外阴影； */
			box-shadow: 0 15px 30px  rgba(0, 0, 0, .4);
			
}
~~~