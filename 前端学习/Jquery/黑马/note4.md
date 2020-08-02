# jQuery补充知识点

## 隐式迭代

### 基本概念

> 隐式迭代：jQuery在设置属性时会自动的遍历，因此我们不需要再遍历

1. jQuery在执行设置性操作时，会给所有的元素都设置上相同的值。
2. jQuery在执行获取性操作时，只会返回第一个元素对应的值。
3. 如果想要给每一个元素都设置不同的值，需要手动进行遍历jQuery对象。

### each方法

> 遍历jQuery对象集合，为每个匹配的元素执行一个函数

语法：

```javascript
// 参数一表示当前元素在所有匹配元素中的索引号
// 参数二表示当前元素, 在function中this也表示当前元素。（Dom对象）
$(selector).each(function(index,element){});
```

【案例：不同的透明度.html】

## 链式编程

> 链式编程的原理：设置性操作会返回一个jQuery对象，因此可以继续调用jQuery的方法。

1. 设置操作的时候，可以使用链式编程。
2. 获取操作的时候，无法使用链式编程。

```javascript
// prevObject 是一个属性，作用：找到上一次返回的jq对象
// end();   将prevObject属性进行封装了
end(); // 筛选选择器会改变jQuery对象的DOM对象，想要回复到上一次的状态，并且返回匹配元素之前的状态。
```

【案例：五角星评分案例.html】

```javascript
prevAll();//获取前面所有的兄弟元素
nextAll();//获取后面所有的兄弟元素
siblings();//获取所有的兄弟元素
prev();//获取前一个兄弟
next();//获取后一个兄弟。
```

## 多库共存

> jQuery使用`$`作为标示符，但是如果与其他框架中的$冲突时，jQuery可以释放`$`符的控制权.

```javascript
var c = $.noConflict();//释放$的控制权,并且把$的能力给了c
```

# jQuery插件

> 插件：jquery不可能包含所有的功能，我们可以通过插件扩展jquery的功能。

jQuery有着丰富的插件，使用这些插件能给jQuery提供一些额外的功能。

## 使用插件

```javascript
1. 引入jQuery文件
2. 引入插件（如果有用到css的话，需要引入css）
3. 使用插件
```

常用插件的使用

+ jquery.color.js的使用
  + https://github.com/jquery/jquery-color


+ jquery.lazyload.js的使用
  + https://github.com/tuupola/jquery_lazyload
+ jquery.ui插件的使用

## 制作jQuery插件

> 制作jQuery插件的核心思想：给jQuery的原型增加方法即可。

```javascript
$.fn.pluginName = function(){}
```

+ 制作基本的jQuery插件
+ 制作手风琴插件

# jQuery框架封装
