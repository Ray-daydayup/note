## 定义

**`instanceof`** **运算符**用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上。



## 语法

```
object instanceof constructor
```

### 参数

- `object`

  某个实例对象

- `constructor`

  某个构造函数

## 描述

`instanceof` 运算符用来检测 `constructor.prototype `是否存在于参数 `object` 的原型链上。

## 代码实现

循环调用查找

```js
function myInstanceOf(obj, constructor) {
  if ((typeof obj !== 'object' && typeof obj !== 'function') || obj === null) {
    return false
  }
  const proto = Object.getPrototypeOf(obj)
  if (proto === constructor.prototype) {
    return true
  } else {
    return myInstanceOf(proto, constructor)
  }
}

console.log(myInstanceOf([], Array)) // true
console.log(myInstanceOf(false, Array)) // true
```

