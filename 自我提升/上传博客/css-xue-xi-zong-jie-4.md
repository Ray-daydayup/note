# css学习总结（4）

## 浮动
```
float的基本取值：
			left 左浮动
			right 右浮动
			none 取消浮动
```

### 行内块布局的弊端
```
弊端：兼容性问题
		1. 行内块元素在低版本浏览器里面不支持  6， 7 
		2. 行内块有默认的间距
		3. 行内块和文字的对齐比较啰嗦
```

### 布局的三种基本方式：

1. 标准流  按照标签默认的特性摆放盒子即为标准流
    - 标准流！或者普通流。普通流实际上就是一个网页内标签元素正常从上到下，从左到右排列顺序的意思，比如块级元素会独占一行，行内元素会按顺序依次前后排列；按照这种大前提的布局排列之下绝对不会出现例外的情况叫做普通流布局。
2. 浮动流  利用浮动摆放盒子即为浮动流
3. 定位流  利用定位摆放盒子即为定位流

### 浮动的意义

1. 浮动最开始是做图文绕排的。
2. 浮动的布局应用场景：让元素直接排成一排，或者一个靠左一个靠右

```
浮动首先创建包含块的概念（包裹）。就是说， 浮动的元素总是找理它最近的父级元素对齐。但是不会超出内边距的范围。 
浮动的元素排列位置，跟上一个元素（块级）有关系。如果上一个元素有浮动，则A元素顶部会和上一个元素的顶部对齐；如果上一个元素是标准流，则A元素的顶部会和上一个元素的底部对齐。
```

### 浮动的特性
```
浮动的特性：
		1. 浮动的元素会“脱标” 不在占有标准流的位置 
		
		2. 因为浮动元素会脱离标准流，会盖住后面的标准流 （兄弟元素）所以 使用浮动的时候有一个基本口诀：要浮动 都浮动 （兄弟元素）
		3. 不会继承
		4. 父容器撑不开
```
1. 浮动的元素会“脱标”不再占用标准流的位置
2. 脱标的元素拥有行内块的表现

**脱标：（脱离了标准流）**

```
标准流：块元素单独占一行，行内元素可以排一排的这种默认的盒子排列方式就是标准流 （按照默认的规则排列的）
1. 脱标的元素不占标准流的位置
2. 不会继承父级的的宽度，内容有多个就撑多大 （不论前身块级还是行内）宽如果超过父元素，会折行（解决:white-space:nowarp或直接写宽）
3. 可以直接写宽高 （不论前身块级还是行内）
4. margin:auto对于脱标元素不起作用
```
浮动因为脱标的特性（脱离标准流了，不占位置，会盖住其他的标准流的盒子），
所以，在使用上有一个口诀：**要浮全浮**（要浮动的话兄弟元素都浮动）

其他细节特性：
```
1. 浮动是在盒子内容区域浮动，不会超出padding区域（水平方向）
2. 浮动的盒子一排装不下的时候会掉下来（掉下来的位置会根据上一个浮动盒子的高度决定，会以上一个盒子的底边线为基准线水平移动，如果在移动的过程里面被挡住了，那么就会卡在这里地方）
3. 右浮动会颠倒盒子顺序
4. 浮动的盒子压不住文字和图片
5. 尽量在标准流的盒子里面浮动
```
### 闭合浮动

> 浮动带来的问题：浮动元素撑不开父级容器

解决办法（闭合浮动）：
```
1. 强行给父盒子添加高度 （不推荐，不利于后期代码的维护 ）
2. 创建一个新的块级盒子放在浮动元素的最后面，给这个盒子添加一个css属性：clear:both;（不推荐，会产生一个多余的盒子）
3. 用伪元素闭合浮动 （推荐，书写一个公共类就可以使用）
4. 给父元素添加overflow:hidden; (在某些特定场景下使用不了)
--------
闭合浮动（清除浮动）
解决浮动带来的撑不开父级容器的问题

解决方案：
1、（定高法）强制给父元素添加一个固定的高   不推荐   因为这样不利于后期代码的维护
2、（额外标签法）在浮动元素的后面追加一个空的块级标签  这个标签用来clear:both;  不推荐：会产生一个空的盒子
3、（伪元素清除浮动法）利用伪元素代替那个空的盒子 因为伪元素是由css渲染 所以不会增大页面的开销
伪元素：在当前标签的内容的前面或者后面追加一个新的“虚拟”标签
伪元素一定是当前标签的子集元素
```

#### 清除浮动本质

清除浮动主要为了解决父级元素因为子级浮动引起内部高度为0 的问题。
![1572188667888](_v_images/20200313150935902_1193.jpg)
![1572188685533](_v_images/20200313150958352_30130.jpg)

#### 清除浮动的方法

其实本质叫做闭合浮动更好一些, 记住，清除浮动就是把浮动的盒子圈到里面，让父盒子闭合出口和入口不让他们出来影响其他元素。

在CSS中，clear属性用于清除浮动，其基本语法格式如下：

```
选择器{clear:属性值;}
```

| 属性值   | 描述                    |
| ----- | --------------------- |
| left  | 不允许左侧有浮动元素（清除左侧浮动的影响） |
| right | 不允许右侧有浮动元素（清除右侧浮动的影响） |
| both  | 同时清除左右两侧浮动的影响         |

##### 额外标签法

```html
是W3C推荐的做法是通过在浮动元素末尾添加一个空的标签例如 <div style=”clear:both”></div>，或则其他标签br等亦可。
```

优点： 通俗易懂，书写方便
缺点： 添加许多无意义的标签，结构化较差。  我只能说，w3c你推荐的方法我不接受，你不值得拥有。。。

##### 父级添加overflow属性方法

可以通过触发BFC的方式，可以实现清除浮动效果。（BFC后面讲解）

~~~css
可以给父级添加： overflow为 hidden|auto|scroll  都可以实现。
~~~
优点：  代码简洁
缺点：  内容增多时候容易造成不会自动换行导致内容被隐藏掉，无法显示需要溢出的元素。

##### 使用after伪元素清除浮动

**:after 方式为空元素的升级版，好处是不用单独加标签了** 

使用方法：

```css
 .clearfix:after {  content: "."; display: block; height: 0; clear: both; visibility: hidden;  }   

 .clearfix {*zoom: 1;}   /* IE6、7 专有 */
```

优点： 符合闭合浮动思想  结构语义化正确
缺点： 由于IE6-7不支持:after，使用 zoom:1触发 hasLayout。
代表网站： 百度、淘宝网、网易等
**注意： content:"."  里面尽量跟一个小点，或者其他，尽量不要为空，否则再firefox 7.0前的版本会有生成空格。**


##### 使用before和after双伪元素清除浮动

使用方法：

```css
.clearfix:before,.clearfix:after { 
  content:"";
  display:table;  /* 这句话可以出发BFC BFC可以清除浮动,BFC我们后面讲 */
}
.clearfix:after {
 clear:both;
}
.clearfix {
  *zoom:1;
}
```

优点：  代码更简洁
缺点：  由于IE6-7不支持:after，使用 zoom:1触发 hasLayout。
代表网站： 小米、腾讯等


### 伪元素

> 就是在当前元素内容的前面或者后面追加一个盒子 这个盒子由css渲染		
     /* 伪元素本质上是一个元素（标签，节点）
		概念：伪元素是在当前元素的内容的前面或者后面追加一个"虚拟的"标签 
		
		特点:由css渲染  所以不会增大页面的开销

		1. 伪元素默认是行内元素   但是可以进行转换
		2. content这个属性不能省略 即使里面没有内容也要写一个空

伪元素特性：
```
1、伪元素由css渲染  所以不会增加你的DOM（html结构）开销
2、伪元素默认是行内元素 可以进行转块等处理
3、伪元素不管有没有内容  content这个值一定不能少 即使没有 也要写个空
4、伪元素 官方推荐写::  但是为了兼容考虑 写成单冒号
5、因为伪元素是css渲染  所以JS获取不到
```
伪元素清除浮动完整代码：
```css
.clearfix::after {
	content:'';
	display:block;
	clear:both;
	height:0;
	visibility:hidden;
}
.clearfix {
	*zoom:1;
}
或者
.clearfix:before,
.clearfix:after { 
	content:"";
	display:table;
}
.clearfix:after {
	clear:both;
}
/* 为了兼容IE6,7 */
.clearfix {
	*zoom:1;
}
```

```
.box2:after {
			//这个不能省略
			content: '';
			//闭合浮动核心属性
			clear: both;
			//因为闭合浮动必须是块级 而伪元素默认是行内 所以 需要转换成块级元素
			display: block;
		} 

**公共类清除浮动：**
------css------
 .clearfix:after {
      /* 这个不能省略 */
      content: "";
      /* 闭合浮动核心属性 */
      clear: both;
      /* 因为闭合浮动必须是块级 而伪元素默认是行内 所以 需要转换成块级元素 */
      display: block;
      height: 0;
      visibility: hidden;
    }
--------html-----
<div class="box clearfix">
    <div class="info"></div>
    <div class="info"></div>
    <div class="info"></div>
    <div class="info"></div>
    <!-- <div class="clear"></div> -->
  </div>
    
```

## 版心和布局流程

阅读报纸时容易发现，虽然报纸中的内容很多，但是经过合理地排版，版面依然清晰、易读。同样，在制作网页时，要想使页面结构清晰、有条理，也需要对网页进行“排版”。

“版心”(可视区) 是指网页中主体内容所在的区域。一般在浏览器窗口中水平居中显示，常见的宽度值为960px、980px、1000px、1200px等。

### 布局流程

为了提高网页制作的效率，布局时通常需要遵守一定的布局流程，具体如下：

1. 确定页面的版心（可视区）。
2. 分析页面中的行模块，以及每个行模块中的列模块。
3. 制作HTML结构 。
4. CSS初始化，然后开始运用盒子模型的原理，通过DIV+CSS布局来控制网页的各个模块。

### 一列固定宽度且居中

![1572188722852](_v_images/20200313150821886_20936.jpg)


最普通的，最为常用的结构

### 两列左窄右宽型

![1572188752577](_v_images/20200313150807089_8669.jpg)


比如小米    <a href="http://www.mi.com" target="_blank"> 小米官网 </a>

### 通栏平均分布型
![1572188788518](_v_images/20200313150854832_19207.jpg)


比如锤子    <a href="http://www.smartisan.com/" target="_blank"> 锤子官网 </a>
