## 属性简写

### ES5

```js
var x = 0, y = 0;
var obj = { x: x, y: y };
```
### ES6

在 ES6 之后是可以用简写的形式来表达：

```js
let x = 0; let y = 0
let obj = { x, y }
```
        
## Computed Property Names

在 `ES6` 之前 `Object` 的 `key` 在定义时必须是字符串，如果想增加“动态”的 `key`，必须是先计算出 `key`，利用 `object[key] = value` 的方式来修改；

在 ES6 之后可以直接用变量或者表达式来定义 `key`。

```js
let obj = {
          foo: 'bar',
          ['baz'+ quux()]: 42
        }
```

而不用在这样写

```js
var obj = {
          foo: 'bar'
        }
obj['baz' + quux()] = 42
```
        
## Method Properties

从 ES6 开始对象内的方法可以简写，包括常规函数和异步函数。

```js
let obj = {
    foo (a, b) {
        
    },
    bar (x, y) {
        
    },
    * quux (x, y) {
        
    }
}
```
        
在 ES5 时代只能这样写

```js
var obj = {
    foo: function (a, b) {
        
    },
    bar: function (x, y) {
        
    }
          //  quux: no equivalent in ES5
}
```

**ES5 时代 Object 的属性只支持常规函数，对于异步函数是不支持的**

