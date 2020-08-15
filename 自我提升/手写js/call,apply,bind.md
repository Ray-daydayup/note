## `call()` 定义

`call()` 方法使用一个指定的 `this` 值和单独给出的一个或多个参数来调用一个函数。

**注意：**该方法的语法和作用与 `apply()`方法类似，只有一个区别，就是 `call()` 方法接受的是**一个参数列表**，而 `apply()` 方法接受的是**一个包含多个参数的数组**。


### 语法

```
fun.call(thisArg, arg1, arg2, ...)
```

### 参数

- `thisArg`

  在 *`fun`* 函数运行时指定的 `this` 值*。if(thisArg == undefined|null) this = window*，if(thisArg == number|boolean|string) this == new Number()|new Boolean()| new String()

- `arg1, arg2, ...`

  指定的参数列表。

## 返回值

使用调用者提供的 `this` 值和参数调用该函数的返回值。若该方法没有返回值，则返回 `undefined`。

## 描述

`call()` 允许为不同的对象分配和调用属于一个对象的函数/方法。

`call()` 提供新的 **this** 值给当前调用的函数/方法。你可以使用 `call` 来实现继承：写一个方法，然后让另外一个新的对象来继承它（而不是在新对象中再写一次这个方法）。

```js
function myCall(thisArg, ...args) {
  thisArg || (thisArg = window)
  let thisObj
  if (typeof thisArg === 'function') {
    throw new TypeError('类型错误')
  }
  if (typeof thisArg === 'object') {
    thisObj = thisArg
  }
  if (typeof thisArg === 'number') {
    thisObj = new Number(thisArg)
  }
  if (typeof thisArg === 'string') {
    thisObj = new String(thisArg)
  }
  if (typeof thisArg === 'boolean') {
    thisObj = new Boolean(thisArg)
  }
  thisObj.fn = this
  const result = thisObj.fn(...args)
  delete thisObj.fn
  return result
}

Function.prototype.myCall = myCall

const obj = {
  a: 1,
  b: 2,
  say(a, b) {
    console.log(this.a, a, b)
  }
}

var a = 'window'

const obj1 = {
  a: 'obj1'
}

obj.say.call(obj1) //obj1 2 2
obj.say.myCall(obj1) // obj1 2 2
obj.say.call(null) // window 2 2
obj.say.myCall(null) //window 2 2
```

## `bind()`定义

bind()方法创建一个新的函数，在bind()被调用时，这个新函数的this被bind的第一个参数指定，其余的参数将作为新函数的参数供调用时使用。

### 语法

`function.bind(thisArg[,arg1[,arg2[, ...]]])`

### 参数

`thisArg`
调用绑定函数时作为`this`参数传递给目标函数的值。 如果使用`new`运算符构造绑定函数，则忽略该值。当使用`bind`在`setTimeout`中创建一个函数（作为回调提供）时，作为`thisArg`传递的任何原始值都将转换为`object`。如果`bind`函数的参数列表为空，执行作用域的`this`将被视为新函数的`thisArg`。
`arg1, arg2, ...`
当目标函数被调用时，预先添加到绑定函数的参数列表中的参数。

### 返回值

返回一个原函数的拷贝，并拥有指定的`this`值和初始参数。

```js
function myBind(thisArg) {
  const fn = this
  return function (...args) {
    return fn.apply(thisArg, args)
  }
}

Function.prototype.myBind = myBind

const obj = {
  a: 1,
  b: 2,
  say(a, b) {
    console.log(this.a, a, b)
  }
}

const obj1 = {
  a: 'obj1',
  callTest() {
    obj.say.bind()(2, 2) // window 2 2
    obj.say.myBind()(2, 2) // window 2 2
  }
}

var a = 'window'

obj.say.bind(obj1)(2, 2) //obj1 2 2
obj.say.myBind(obj1)(2, 2) // obj1 2 2
obj.say.bind()(2, 2) // window 2 2
obj.say.myBind()(2, 2) //window 2 2

obj1.callTest()
```

