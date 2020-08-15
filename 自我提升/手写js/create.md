## `Object.create()`

`Object.create()`方法创建一个新对象，使用现有的对象来提供新创建的对象的`__proto__`。 

### 语法

`Object.create(proto[, propertiesObject])`

### 参数

- `proto`

  新创建对象的原型对象。

- `propertiesObject`

  可选。如果没有指定为 [`undefined`](db35d98c3293670a2e3d56d18027152b.html)，则是要添加到新创建对象的可枚举属性（即其自身定义的属性，而不是其原型链上的枚举属性）对象的属性描述符以及相应的属性名称。这些属性对应[`Object.defineProperties()`](ce039541c6d1812f856050bc206718a6.html)的第二个参数。

### 返回值

一个新对象，带着指定的原型对象和属性。

### 例外

如果`propertiesObject`参数是 [`null`](ef31ab67c58d5a673779aa12ff3f929c.html) 或非原始包装对象，则抛出一个 [`TypeError`](d00a68fa97e570f49e3185e301d6b56b.html) 异常。



## 代码实现

```js
function myCreate(obj) {
  function F() {}
  F.prototype = obj
  return new F()
}

const obj = {
  a: 1,
  b: 2,
  say() {
    console.log('hi')
  }
}

const newObj = myCreate(obj)
console.log(newObj)//{}
newObj.say()//hi
```

