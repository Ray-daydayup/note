### 1-1.使用场景

当需要创建多个对象的时候，如果循环会**「重复创建」**很多变量，占用内存。 如果用new生成，那么里面重复的属性是在**「原型」**上，就不用占用内存。

### 1-2.意义

节省代码，属于语法糖，可以拥有使用构造函数里面的所有**「属性和方法」**，并且还可以**「拓展」**。

### 1-3.实现步骤

1. 传入参数为：构造函数和函数参数
2. 创建1个空对象
3. 使空对象的`__proto__`指向构造函数的原型(`prototype`)
4. 将`this`绑定到空对象上，执行构造函数
5. 判断构造函数的返回值，如果是对象，直接返回这个值。如果不是，就返回开始创建的对象

### 1-4.代码实现

```js
function myNew(fn, ...args) {
  const obj = Object.create(fn.prototype)
  fn.apply(obj, args)
  return obj
}

function Test(a, b) {
  this.a = a
  this.b = b
  this.c = 'test'
}
Test.prototype.say = function () {
  console.log(this.a, this.b, this.c)
}

const obj = new Test(1, 2)
const obj1 = myNew(Test, 1, 2)

console.log('obj :>> ', obj) // obj :>>  Test {a: 1, b: 2, c: "test"}
console.log('obj1 :>> ', obj1) // obj1 :>>  Test {a: 1, b: 2, c: "test"}

obj.say() // 1 2 "test"
obj1.say() // 1 2 "test"

```

