## Array

### 数组遍历

#### ES5中数组遍历

1. `for`循环

```js
const arr = [1, 2, 3, 4, 5]
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i])
}
```

2. `forEach` 语法简洁很多，不需要通过索引去访问数组项，但不支持 `break`、`continue` 等。

```js
arr.forEach(function (item) {
  console.log(item)
})
//输出是 1,3,4,5。return跳过
[1,2,3,4,5].forEach(function(i){
    if(i===2){
        return;
    }else{
        console.log(i)
    }
})
```

3. `every` 使用 `every` 遍历就可以做到 `break` 那样的效果，简单的说 `return false` 等同于 `break`，`return true` 等同于 `continue`。如果不写，默认是 `return false`。`every` 的代码块中不能使用` break`、`continue`，它会抛出异常。

```js
[1,2,3,4,5].every(function(i){
    if(i===2){
        return false;
    }else{
        console.log(i)
        return true
    }
})
```

4. `for...in` 确实可以遍历数组，而且还支持` continue`、`break`等功能。但如果`array` 有自定义属性，也会被遍历出来。这是因为 `for…in` 是为遍历对象创造的，不是为数组设计的。`for…in`代码块中不能有 `return`，不然会抛出异常。

```js
for (var index in array) {
    console.log(array[index]);
}
```
**数组索引是字符串**

5. 等等

#### ES6数组遍历

1. `for…of` 支持 `break`、`continue`、`return`的，所以在功能上非常贴近原生的 `for`。（**遍历**）

```js
for (let val of [1,2,3]) {
    console.log(val);
}
// 1,2,3
```

看下这个伪代码，`of`后面是 `iterable` 既不是 `for` 循环规定的 `array`，也不是 `for…in` 规定的 `Object`，而是 `iterable`。如果查查 `iterable` 的含义就很直观的感受到 `for…of` **遍历的是一切可遍历的元素**（数组、对象、集合）等，不要小瞧这个功能，因为在 ES6 中允许开发者自定义遍历，换句话说任何数据结构都可以自定义一个遍历，这个遍历是不能被 `for、for…in` 理解和实现的。很抽象吧？我们来描述个场景吧。

客户：老板给我来瓶最便宜的啤酒、最便宜的火腿肠、最便宜的打火机，多少钱？
商户：稍等，我算下

作为程序员的我们需要考虑下数据结构，首先定一个价格对象 Price，然后为每个商品 A、B、C等定价，最后是根据用户的选择，输入系统并计算。
在 ES6 中新增了很多实用的原生 API，方便开发者对 Array 的操控性更强，如 for…of、from、of、fill、find、findIndex等。

```js
const Price = {
          A: [1.5, 2.3, 4.5],
          B: [3, 4, 5],
          C: [0.5, 0.8, 1.2]
        }
```

然后给这个**数据结构自定义遍历规则(Iterator)**，希望对这个遍历之后可以输出 1.5、3、0.5。（先不要关心 Iterator 是怎么实现的，ES6的新增语法，后面会讲）。
面对这样的数据结构，for、forEach、every、for…in 都无能为力吧？使用 for…of 就木有问题，它就是为解决这种问题而生。


- **JavaScript 世界里有哪些元素是可遍历的？**
- **如何给数据结构自定义遍历？**

### 数组转换

- **伪数组转换成数组**

数组是开发中经常用到的数据结构，它非常好用。在 `JavaScript` 的世界里有些对象被理解为数组，然而缺不能使用数组的原生 `API`，比如函数中的 `arguments`、`DOM`中的 `NodeList`等。当然，**还有一些可遍历的对象，看上去都像数组却不能直接使用数组的 `API`**，因为它们是伪数组（`Array-Like`）。

伪数组具备两个特征：

1. 按索引方式储存数据 
2. 具有`length`属性；如：
3. 
```js
let arrLike = {
    0: ‘a’,
    1: ‘b’,
    2: ‘c’,
    length: 3
}
```

#### ES5数组转换

`ES5`伪数组使用数组的 `API` 就要想办法把它们转化为数组，传统的做法是这样的：

```js
let args = [].slice.call(arguments);
let imgs = [].slice.call(document.querySelectorAll('img'));
```
基本原理是使用 call 将数组的 api 应用在新的对象上，换句话说是利用改变函数的上下文来间接使用数组的 api。

#### ES6数组转换

- `Array.prototype.from()`
 
1. 在 ES6 中提供了新的 api 来解决这个问题，就是 Array.from，代码如下：

```js
let args = Array.from(arguments);
let imgs = Array.from(document.querySelectorAll('img'));
```
2. `Array.from` 的几个参数：

语法：`Array.from(arrayLike[, mapFn[, thisArg]])`

|    参数    |                       含义                       | 必选 |
| :--------: | :---------------------------------------------: | :-: |
| arrayLike	 |       想要转换成数组的伪数组对象或可迭代对象       |   Y  |
|   mapFn    | 如果指定了该参数，新数组中的每个元素会执行该回调函数 |  N   |
|  thisArg   |     可选参数，执行回调函数 mapFn 时 this 对象      |  N   |

看了这几个参数至少能看到 `Array.from` 还具备 `map` 的功能，比如我们想初始化一个长度为 5 的数组，每个数组元素默认为 1，之前的做法是这样的：

```js
let arr = Array(6).join(' ').split('').map(item=>1)// [1,1,1,1,1]
```
这样写虽然也能实现，但是用起来比较繁琐，使用 Array.from 就会简洁很多。

```js
Array.from({ length: 5 }, function () { return 1 })
```

- **生成长度为 5 ，为何用的是 Array(6)**?

### 创建新数组

#### ES5创建新数组

```js
let arr = Array(5);
let arr2 = [];
```
#### ES6创建新数组

1. `Array.prototype.of()`

- `Array.of()` 方法创建一个具有可变数量参数的新数组实例，而不考虑参数的数量或类型。
- `Array.of()` 和 `Array` 构造函数之间的区别在于处理整数参数：`Array.of(7)` 创建一个具有单个元素 7 的数组，而 `Array(7)` 创建一个长度为7的空数组（注意：这是指一个有7个空位(`empty`)的数组，而不是由7个`undefined`组成的数组）。

```js
Array.of(7);       // [7]
Array.of(1, 2, 3); // [1, 2, 3]
Array(7);          // [ , , , , , , ]
 Array(1, 2, 3);    // [1, 2, 3]
```
        
- 语法：`Array.of(element0[, element1[, …[, elementN]]])`

|   参数   |                 含义                  | 必选 |
| :------: | :----------------------------------: | :-: |
| elementN | 任意个参数，将按顺序成为返回数组中的元素 |  Y   |

2. `Array.prototype.fill()`

- `fill()` 方法用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。**不包括终止索引**。

```js
let array = [1, 2, 3, 4]
array.fill(0, 1, 2)// [1,0,3,4]
```
        
- 这个操作是将 array 数组的第二个元素（索引为1）到第三个元素（索引为2）内的数填充为 0，不包括第三个元素，所以结果是 [1,0,3,4]

- **技巧:**
我们前面有提到用 Array.from 初始化为一个长度固定，元素为指定值的数组。

```js
Array(5).fill(1)  // [1,1,1,1,1]
```
        
- **fill 不具备遍历的功能，它是通过指定要操作的索引范围来进行，通过这道题目可以看出不指定索引会对所有元素进行操作**

- 语法：`arr.fill(value[, start[, end]])`

|  参数  |              含义               | 必选 |
| :----: | :----------------------------: | :-: |
| value  |       用来填充数组元素的值       |   Y  |
| start	 |       起始索引，默认值为0        |  N   |
|  end   | 终止索引，默认值为 `this.length` |   N  |

### 数组查找元素

#### ES5数组查找元素

- `filter` 返回满足条件的**所有元素**的数组，通过判断数组长度来判断是否存在否元素
- 如果仅仅判断是否存在某元素，当数组维度很大的时候，则会影响性能(因为会遍历全部)

```js
let newArr = arr.filter(function(item){
    return item==2
})
```

#### ES6数组查找元素

1. `Array.prototype.find()`

- `find()` 方法返回数组中满足提供的测试函数的第一个元素的值，否则返回 `undefined`

```js
let array = [5, 12, 8, 130, 44];
let found = array.find(function(element) {
                return element > 10;
            });
console.log(found); // 12
```
        
- 语法：`arr.find(callback[, thisArg])`

|    参数    |                            含义                             | 必选 |
| :--------: | :--------------------------------------------------------: | :-: |
|  callback  | 在数组每一项上执行的函数，接收 3 个参数，element、index、array |   Y  |
| thisArg	 |                  执行回调时用作 this 的对象                  |  N   |

- `find()` 和 `ES5` 的 `filter()` 有什么区别？


2. `Array.prototype.findIndex()`

- `findIndex()`方法返回数组中满足提供的测试函数的第一个元素的索引。否则返回-1。其实这个和 `find()` 是成对的，不同的是它返回的是索引而不是值。

```js
let array = [5, 12, 8, 130, 44];
let found = array.find(function(element) {
          return element > 10;
        });
console.log(found);// 1
```
        
- 语法：`arr.findIndex(callback[, thisArg])`

|    参数    |                            含义                             | 必选 |
| :--------: | :--------------------------------------------------------: | :-: |
|  callback  | 在数组每一项上执行的函数，接收 3 个参数，element、index、array |   Y  |
| thisArg	 |                  执行回调时用作 this 的对象                  |  N   |

- **JavaScript 世界里有哪些元素是可遍历的？**
- **如何给数据结构自定义遍历？**
- **find() 和 ES5 的 filter() 有什么区别？**


**阅读:**
[MDN文档的Array](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)