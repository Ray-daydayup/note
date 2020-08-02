# jQuery操作样式

## css操作

> 功能：设置或者修改样式，操作的是style属性。


> 操作单个样式

```javascript
//name：需要设置的样式名称
//value：对应的样式值
css(name, value);
//使用案例
$("#one").css("background","gray");//将背景色修改为灰色

```

> 设置多个样式

```javascript
//参数是一个对象，对象中包含了需要设置的样式名和样式值
css(obj);
//使用案例
$("#one").css({
    "background":"gray",
    "width":"400px",
    "height":"200px"
});
// 推荐还是使用驼峰式的写法

```

> 获取样式

```javascript
//name:需要获取的样式名称
css(name);
//案例
$("div").css("background-color");

```

>**总结**

```js
css(name, value)   ==>   设置单个样式
            css(obj)           ==>   设置多个样式
            进行设置性操作的时候，会给获取到的所有元素都设置上相同的样式
            css(name)          ==>   获取元素样式
            进行获取性操作的时候，获取第一个元素的对应的样式值即可
```
注意：获取样式操作只会返回第一个元素对应的样式值。

隐式迭代：
1. 设置操作的时候，如果是多个元素，那么给所有的元素设置相同的值
2. 获取操作的时候，如果是多个元素，那么只会返回第一个元素的值。



## class操作

> 添加样式类

```javascript
//name：需要添加的样式类名，注意参数不要带点.
addClass(name);
//例子,给所有的div添加one的样式。
$(“div”).addClass(“one”);

```

> 移除样式类

```javascript	
//name:需要移除的样式类名
removeClass(“name”);
//例子，移除div中one的样式类名
$(“div”).removeClass(“one”);

```

> 判断是否有某个样式类（只要 有一个元素有指定的类名，就返回true）

```javascript
//name:用于判断的样式类名，返回值为true false
hasClass(name)
//例子，判断第一个div是否有one的样式类
$(“div”).hasClass(“one”);

```

> 切换样式类

```javascript
//name:需要切换的样式类名，如果有，移除该样式，如果没有，添加该样式。
toggleClass(name);
//例子
$(“div”).toggleClass(“one”);

```

【案例：tab栏切换案例.html】

【案例：旋转木马.html】

# jQuery操作属性

## attr操作

> 设置单个属性

```javascript
//第一个参数：需要设置的属性名
//第二个参数：对应的属性值
attr(name, value);
//用法举例
$(“img”).attr(“title”,”哎哟，不错哦”);
$(“img”).attr(“alt”,“哎哟，不错哦”);

```

> 设置多个属性

```javascript
//参数是一个对象，包含了需要设置的属性名和属性值
attr(obj)
//用法举例
$("img").attr({
    title:"哎哟，不错哦",
    alt:"哎哟，不错哦",
    style:"opacity:.5"
});

```

> 获取属性

```javascript
//传需要获取的属性名称，返回对应的属性值
attr(name)
//用法举例
var oTitle = $("img").attr("title");
alert(oTitle);

```

> 移除属性

```javascript
//参数：需要移除的属性名，
removeAttr(name);
//用法举例
$("img").removeAttr("title");

```

【案例：美女相册.html】

## prop操作

> 在jQuery1.6之后，对于checked、selected、disabled这类boolean类型的属性来说，不能用attr方法，只能用prop方法。

```javascript
//设置属性
$(“:checked”).prop(“checked”,true);
//获取属性
$(“:checked”).prop(“checked”);//返回true或者false

```
`:checked ` 获取被选中的表单元素
【案例：表格全选案例.html】

# jQuery动画

> jquery提供了三组基本动画，这些动画都是标准的、有规律的效果，jquery还提供了自定义动画的功能。【演示动画例子】

## 三组基本动画

> 显示(show)与隐藏(hide)是一组动画：
> 滑入(slideUp)与滑出(slideDown)与切换(slideToggle)，效果与卷帘门类似
> 淡入(fadeIn)与淡出(fadeOut)与切换(fadeToggle)

```javascript
show([speed], [callback]);
//speed(可选)：动画的执行时间
	 //1.如果不传，就没有动画效果。如果是slide和fade系列，会默认为normal
	 //2.毫秒值(比如1000),动画在1000毫秒执行完成(推荐)
     //3.固定字符串，slow(600)、normal(400)、fast(400)，如果传其他字符串，则默认为normal。
//callback(可选):执行完动画后执行的回调函数
```
--------------------------------------
- show(speed, easing, callback)
- hide(speed, easing, callback)
- toggle(speed, easing, callback)
- 不传参，就没有动画效果
- 主要修改的时候元素的width、height、opacity
- 三个参数都是可选的
            
**参数：**

- speed： 动画时长，单位是ms，默认还提供了三个字符串的值，可选
    - "slow": 600
    - "normal": 400
    - "fast": 200
    - 如果传递的是除了上面三个之外的字符串，取normal
- easing: 动画效果，默认值是swing（秋千，慢-快-慢），linear（匀速），可选
- callback: 动画结束的时候来执行的函数,可选
--------------------------------------
- slideDown(speed, easing, callback)
- slideUp(speed, easing, callback)
- slideToggle(speed, easing, callback)
    - 不传参，有动画效果，normal = 400
    - 参数和show方法一样，主要是动画效果不同
--------------------------------------


【案例：下拉菜单动画版.html】
【案例：京东轮播图(呼吸灯).html】

## 自定义动画

> animate: 自定义动画
> jq提供的animate默认只支持做数字型的动画，不支持颜色的动画

```javascript
$(selector).animate({params},[speed],[easing],[callback]);
// {params}：要执行动画的CSS属性，带数字（必选）
// speed：执行动画时长（可选）
// easing:执行效果，默认为swing（缓动）  可以是linear（匀速）
// callback：动画执行完后立即执行的回调函数（可选）

```

## 动画队列与停止动画

> 在同一个元素上执行多个动画，那么对于这个动画来说，后面的动画会被放到动画队列中，等前面的动画执行完成了才会执行（联想：火车进站）。
**动画队列：**

- jq提供了动画队列来实现依次执行动画的效果
- 给元素的动画队列添加多个动画，依次来执行动画队列里面的动画
- 就是链式编程的写法

有了动画队列的好处：

- 依次执行动画
   
坏处：

- 如果元素动画频发的触发，就会不停的去做动画队列的里面的动画

```javascript
//stop方法：停止动画效果 停止当前正在执行的动画，执行后面的动画
stop(clearQueue, jumpToEnd);
//第一个参数：是否清除队列
//第二个参数：是否跳转到最终效果
clearQueue: 是否清除动画队列，true， 清除
jumpToEnd: 跳转到当前正在执行动画的最后效果， true，跳转
有默认值，两个都是false
```
【案例：手风琴特效】
【案例：音乐导航】

# jQuery节点操作

## 创建节点

```javascript
//$(htmlStr)
//htmlStr：html格式的字符串
$(“<span>这是一个span元素</span>”);

```

## 添加节点

- append: 对appendChild() 的封装
- A.append(B);   把B元素添加到A元素里面去，作为最后一个子元素
```js
$("div").append( $(".outerP") );  // 页面上存在的元素，会是剪切的效果
$("div").append( '<p class="outerP">这是div外的p元素</p>' ); // 参数可以是html字符串，先创建元素，在去添加
```

- appendTo
- B.appendTo(A);   把B元素添加到A元素里面去，作为最后一个子元素
```js
$(".outerP").appendTo( $("div") );// 页面上存在的元素，会是剪切的效果 
$(".outerP").appendTo( "div" );  // 参数可以是选择器，先找到这个元素，在去将B添加到A
```


- prepend
- A.prepend(B);   把B元素添加到A元素里面去，作为第一个子元素
```js
$("div").prepend( $(".outerP") );// 页面上存在的元素，会是剪切的效果 
$("div").prepend('<p class="outerP">这是div外的p元素</p>');// 参数可以是html字符串，先创建元素，在去添加
```

- prependTo()
- B.prependTo(A);  把B元素添加到A元素里面去，作为第一个子元素
```js
$(".outerP").prependTo( $("div") );// 页面上存在的元素，会是剪切的效果 
$(".outerP").prependTo( "div" );//参数可以是选择器，先找到这个元素，在去将B添加到A
```

- after()
- A.after(B);    让B元素作为A的兄弟元素，并且放在A的后面
```js
$("div").after( $(".innerP") );//会是剪切的效果 
```

- before()
- A.before(B);   让B元素作为A的兄弟元素，并且放在A的前面
```js
$("div").before( $(".innerP") );//会是剪切的效果 
```

【案例：城市选择案例.html】

## 清空节点与删除节点

> empty：清空指定节点的所有元素，自身保留(清理门户)

```javascript
$(“div”).empty();//清空div的所有内容（推荐使用，会清除子元素上绑定的内容，源码）
$(“div”).html(“”);//使用html方法来清空元素，不推荐使用，会造成内存泄漏，绑定的事件不会被清除。
```

> remove：相比于empty，自身也删除（自杀）

```javascript
$(“div”).remove();
```

## 克隆节点

> 作用：复制匹配的元素

```javascript
// 复制$(selector)所匹配到的元素（深度复制）
//cloneNode(true)
// 返回值为复制的新元素，和原来的元素没有任何关系了。即修改新元素，不会影响到原来的元素。
// 参数： 布尔值，如果为true，表示克隆的元素的事件也会被克隆
//           默认值是false
$(selector).clone();

```

【案例：微博发布】
【案例：弹幕效果】
