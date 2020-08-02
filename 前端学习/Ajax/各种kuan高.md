### Javascript各种宽高 

```javascript
alert(document.documentElement.clientWidth || document.body.clientWidth)  // 可视区域宽度
alert(document.documentElement.clientHeight || document.body.clientHeight)  // 可视区域高度

alert(document.documentElement.offsetWidth || document.body.offsetWidth); // 当前窗口文档宽度
alert(document.documentElement.offsetHeight || document.body.offsetHeight); // 当前窗口文档高度

alert(document.documentElement.scrollTop || document.body.scrollTop); //网页被卷去的Top(滚动条)
alert(document.documentElement.scrollLeft ||document.body.scrollLeft); //网页被卷去的Left(滚动条)
```

### Jquery

**宽高**

```javascript
$(window).width(); // 可视区域宽度
$(window).height(); // 可视区域高度

$(document).width(); // 当前窗口文档宽度
$(document).height(); // 当前窗口文档高度

$(window).scrollTop(); 
$(window).scrollLeft();
```

**offset()**:

>  方法返回或设置匹配元素相对于文档的偏移（位置）。


> 返回第一个匹配元素的偏移坐标。


> 该方法返回的对象包含两个整型属性：top 和 left，以像素计。

```javascript
$('div').offset().left;
```

**position()**:

> 方法返回匹配元素相对于定位父元素的位置（偏移）。


>  该方法返回的对象包含两个整型属性：top 和 left，以像素计。

```javascript
$('div').position().left;
```

