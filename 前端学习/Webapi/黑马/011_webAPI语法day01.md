---
typora-copy-images-to: media
---

> 第02阶段.前端基本功.前端基础.入门语法

# 基础语法

## 学习目标
* 理解
  * 了解DOM中常用的操作
  * 了解document对象
* 重点

  * 掌握DOM中获取元素的常用方法
  * 掌握如何给元素注册事件
  * 掌握DOM中常用的非表单属性

## 1.认识DOM

**概念:  **文档对象模型（Document Object Model，简称DOM），是[W3C](https://baike.baidu.com/item/W3C)组织推荐的处理[可扩展标记语言](https://baike.baidu.com/item/%E5%8F%AF%E6%89%A9%E5%B1%95%E7%BD%AE%E6%A0%87%E8%AF%AD%E8%A8%80)的标准[编程接口](https://baike.baidu.com/item/%E7%BC%96%E7%A8%8B%E6%8E%A5%E5%8F%A3)。

> DOM的设计是以对象管理组织（OMG）的规约为基础的，因此可以用于任何编程语言。Dom技术使得用户页面可以动态地变化，如可以动态地显示或隐藏一个元素，改变它们的属性，增加一个元素等，Dom技术使得页面的交互性大大地增强。

**通俗理解:**  把页面上的内容转换成对象的形式,通过操作对象,达到操作页面上标签和标签属性的一组方法

![1497154623955](media/1497154623955.png)





## 2. DOM 中常用的操作

- 获取元素
- 对元素进行操作(设置其属性或调用其方法)
- 动态创建元素
- 给元素注册事件

## 3. document对象

**概念: **document对象代表在浏览器中加载的页面

```js
  console.log(document);  //整个页面
  console.log(document.documentElement);  //html标签
  console.log(document.body);  //body标签
```

![文档转换成DOM示意图](_v_images/20190921110240268_11574.png )

## 4.获取页面中的元素

>什么是元素?
>
>​	html中的标签在DOM中称为元素
>
>为什么要获取页面上的元素呢?
>
>​	因为:我们想要操作页面上的元素,首先需要获取到对应的元素，然后才能进行后续操作

### 4.1 根据id获取元素

**语法:** document.getElementById('id名');

**作用:** 在整个文档中查找id名为传入的值的元素,如果没有返回null

```javascript
//html
<div id="box"></div>
//js
//在整个文档中查找id为box的元素
var div = document.getElementById('box');
console.log(div);  //返回的是对应的元素
```

### 4.2 根据标签名获取元素

**语法:** document.getElementsByTagName('标签名');

**作用:** 在整个文档中查找所有标签名为传入的值的元素,将这些所有符合条件元素的存放到一个伪数组中返回出来,如果没有就返回一个空的伪数组

```javascript
//html
<div>div1</div>
<div>div2</div>
<div>div3</div>
//js
//在整个文档中查找所有的div标签
var divs = document.getElementsByTagName('div');
for (var i = 0; i < divs.length; i++) {
  var div = divs[i];
  console.log(div);
} 
```

### 小结:
```js
console.log(document.getElementsByTagName('div')); //在整个页面的范围内找
var box = document.getElementById('box');
console.log(box.getElementsByTagName('div'));  //在元素的范围中去找
```
-  总结:
    - 批量获取 :` document/element.getElementsByTagName('标签名') ` 返回一个伪数组,如果找不到返回空的伪数组
    - 通过id获取: `document.getElementById('id名')  `返回具体的元素, 找不到返回null

- 通过document这个对象调用获取元素的方法
- getElementById 返回的是对应的DOM元素, 如果没有返回null
- getElementsByTagName 返回的是存储DOM元素的伪数组,如果没有返回空的伪数组



## 5.事件

### 5.1 什么是事件?

用户点击页面上的某个元素,或者表单上的某个文本框获得焦点等等,这些都是事件
![事件示意](_v_images/20190921113647251_15231.gif =942x)

### 5.2 为什么要学习事件?

因为作为一个网页开发者,我们要实现当用户点击某个元素时,我们要去实现对应的需求,所以我们要学习事件
![事件示意01](_v_images/20190921113740463_22321.gif =942x)

### 5.3 如何实现这种需求呢?

#### 5.3.1 事件的三要素

- 事件源  :   监听事件的元素
- 事件名  :    监听的事件类型
- 事件处理函数 : 触发事件时,要调用的函数

#### 5.3.2 如何注册事件

**语法:** 事件源.on + 事件名 = 事件处理函数

> element.onclick = function(){}

```javascript

//html
<div id="box" style="width:100px; height:100px; background-color: red"><div>
    
//js
var box = document.getelementById('box');
//
box.onclick = function(){
    console.log(1);
}
    
    mouseenter  mouseleave  不会冒泡的(推荐)
```

### 5.4 给a标签注册事件时,需要注意的问题

> a标签是超链接标签,在开发网页中,尽量多的使用a标签有利于网页的SEO,所以我们在做动态效果时,要经常给a标签注册点击事件
>
> 那么给a标签注册事件,到底要注意什么问题呢?

```html
//html 
<a href = "" id = "link">点击在控制台打印数字1</a>
```
```js
//js

var link = document.getElementById('link');
link.onclick = function(){
    console.log(1);
}

//当我们执行了以上代码之后,我们发现在控制台并没有看到数字1.

//原因: 因为a标签是超链接标签,超链接标签默认是用来做页面跳转的,虽然我们没有在href属性中书写正确的url地址,但是当我们点击a标签时,页面也会重新刷新。所以当我们点击a标签时,触发了事件处理函数,并且执行了console.log(1)这行代码,但是当这行代码执行完毕之后,很快a标签的默认行为导致浏览器重新刷新了一遍,由于执行的速度非常快,所以我们肉眼根本看不到控制台上输出的1.

//所以为了解决这个问题,我们需要阻止a标签默认行为执行

link.onclick = function(){
    console.log(1);
    return false; //在事件处理函数中的最后一行写return false,就会阻止a标签默认行为的执行
}

```

## 6. DOM中常用的非表单元素属性

### 6.1 为什么要学习DOM中常用的非表单元素属性?

因为: 我们学习DOM就是为了通过访问元素的属性或者方法,来动态的改变元素的状态,比如,用js修改a标签href属性的值
![操作元素属性](_v_images/20190921113923363_17723.png =1040x)

### 6.2 常用的非表单元素属性有哪些?

- href  超链接的url地址
- title  标签的标题属性
- id      标签的id属性
- src     引入外部文件的路径
- innerText   标签内的文本
- innerHTML   标签内的内容



### 6.3 获取属性的值/设置属性的值

```javascript
//html
<img src = "1.jpg" title="示例" id="img">
    
var img = document.getElementById('img');
console.log(img.title) // 获取属性的值
img.title = '动态修改'  //设置属性的值
img.src = '2.jpg' //这行代码执行之后,会展示2.jpg的图片
```

**案例:** 点击按钮,切换图片



### 6.4 innerText 和 innerHTML

**作用: **  给双标签的元素设置内容/获取双标签里面的内容

**不同点:**

innerText 只是用于获取文本或设置文本

innerHTML 不仅可以用于设置/获取文本,还可以识别html

**相同点:**

如果是赋值的话,都会覆盖元素内本身的内容

**注意: **

- 这两个是用于双标签的属性


## 7. 课后综合练习

美女相册

- **补充**
```html
<!--如果a的href属性里面写了javascript: .这个javascript代表协议. 作用: 改变了a的默认行为, 会执行javascript后面的代码-->
<!--void是js中的一个关键字 作用: 什么都不做-->
<a href="javascript:void(0) ;">点击</a>
<a href="javascript:void 0;">点击</a>
<a href="javascript:;">点击</a>
```

## 8. 扩展内容@

### 8.1 定义id属性的元素,不获取直接使用 

> 由于id名具有唯一性，部分浏览器支持直接使用id名访问元素，但不是标准方式，生产环境下(上线了)不推荐使用。

```javascript
//html
<div id="box"></div>

//js
box.onclick = function(){
    console.log(1);
}
```

### 8.2 获取页面元素的其他方式

#### 8.2.1 根据name属性获取元素 (有兼容问题,不同的浏览器实现不同)

>在IE和Opera中， getElementsByName()  方法还会返回那些id为指定值的元素。

**语法:** document.getElementsByName('name属性的值')

**作用: ** 在整个文档中查找所有name属性值为传入的值的元素,将这些所有符合条件元素的存放到一个伪数组中返回出来,如果没有就返回一个空的伪数组

```javascript
//html
<input type="checkbox" name="hobby">

//js
var inputs = document.getElementsByName('hobby');
//返回一个伪数组
for (var i = 0; i < inputs.length; i++) {
  var input = inputs[i];
  console.log(input);
}
```

#### 8.2.2 根据类名获取元素  (有兼容问题,ie9+支持)

**语法:** `document.getElementsByClassName('类名')`

**作用: ** 在整个文档中查找所有class属性值为传入的值的元素,将这些所有符合条件元素的存放到一个伪数组中返回出来,如果没有就返回一个空的伪数组

```javascript
//html
<div class="main"></div>
    
//js
var mains = document.getElementsByClassName('main');
//返回一个伪数组
for (var i = 0; i < mains.length; i++) {
  var main = mains[i];
  console.log(main);
}
```

#### 8.2.3 根据选择器获取元素  (有兼容问题,ie8+支持)

**语法:**  `document.querySelector('选择器');`

**作用:** 在整个文档中查找所有符合选择器值的元素,但是只返回其中的第一个元素,如果没有返回`null`

**注意:**  如果想要所有符合选择器值的元素,请使用 `querySelectorAll`方法

`querySelectorAll`返回的是一个伪数组,如果没有则返回空的伪数组

```javascript
//html
<div class="element">div1</div>
<div class="element">div2</div>
<div class="element">div3</div>

//js
//在整个文档中,查找类名为element的元素,
var div = document.querySelector('.element');
console.log(div); //返回的是所有符合条件中的第一个

var divs = document.querySelectorAll('.element');
//返回的是一个伪数组, 伪数组中存储了所有符合条件的元素
for (var i = 0; i < divs.length; i++) {
  var box = divs[i];
  console.log(box);
}
```



### 8.3 DOM中元素可以使用的获取元素的方法

```javascript
element.getElementsByTagName('标签名')

element.getElementsByClassName('类名')

element.querySelector('选择器')

element.querySelectorAll('选择器')

//以上这些方法也可以使用获取到的DOM对象调用
//使用document调用这些方法是在整个页面中查找
//使用获取到的DOM对象调用这些方法,是在当前DOM对象的里面查找
console.log(document.getElementsByName('one')); //通过name属性找元素, 返回的是伪数组,找不到是空的伪数组

//console.log(document.getElementsByClassName('one')); //通过类型获取元素.返回伪数组,找不到返回空的伪数组
 var wrap = document.getElementById('wrap');
console.log(wrap.getElementsByClassName('one')); //也可以使用element调用

console.log(document.querySelector('.one')); //通过选择器查找元素 ,返回所有符合条件里面的第一个
console.log(document.querySelectorAll('#wrap div')); //通过选择器查找元素 ,返回一个伪数组
```

```html
<div>中国</div>
<div id="center">
    北京
	<div>昌平</div>
	<div>海淀</div>
</div>
```
```js
var center = document.getElementById('center');
var divs = center.getElementsTagName('div');
console.log(divs); //返回的伪数组中只有昌平和海淀

```

### 8.4 `innerText` 和 `innerHTML`的兼容问题

- innerHTML是非标准属性(非w3c标准),但是所有的浏览器都支持
- innerText属性存在兼容性问题,早期的火狐浏览器不支持该属性,使用textContent替代

