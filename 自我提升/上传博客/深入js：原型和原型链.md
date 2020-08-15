## 前言

翻一翻文档，弄懂一下原型和原型链，从规范的角度去看看原型相关的知识

1. 原型的定义是啥？
2. 原型链是啥？
3. 函数都有``prototype`属性吗？
4. new 到底经历了啥？

## 原型的定义

> ECM-262 : object that provides shared properties for other objects
>
> 为其他对象提供共享属性的对象

再来看ECM-262关于原型的另一段叙述:

>When a constructor creates an object, that object implicitly references the constructor's "prototype" property for the purpose of resolving property references. The constructor's "prototype" property can be referenced by the program expression
>`constructor.prototype`, and properties added to an object's prototype are shared,
>through inheritance, by all objects sharing the prototype. 
>
>Alternatively, a new object may be created with an explicitly specified prototype by using the `Object.create` built-in function.
>
>翻译:
>
>当构造函数constructor 创建一个对象时，该对象隐式引用构造函数的“prototype”属性，以便解析属性引用。
>
>构造函数的“prototype”属性可以被程序表达式`constructor.prototype`引用,并且通过继承,添加到一个对象原型上的属性被所有共享该原型的对象共享。
>
>另外,可以通过调用内置函数`Object.create`显式指定一个原型,来创建一个新对象

## 原型、构造函数和实例对象的关系

那么从上面的描述我们知道以下几点:

1. 构造函数都有一个 `prototype` 属性, `prototype` 属性指向了一个对象，这个对象正是调用该构造函数而创建的**实例**的原型
2. 对象隐式引用`constructor.prototype`,也就是我们的实力对象上存在着一个指针`[[prototype]]`指向这个原型对象,但是这个指针`[[prototype]]`我们是看不到的,但是个浏览器提供了`__proto__`属性.
3. 原型对象上的所有属性是可以被拥有该原型对象的实例对象访问到的

**我们再来看两个特殊的属性：**

1. `constructor`，原型对象的属性，指向对应的构造函数
2. 这是每一个JavaScript对象(除了 null )都具有的一个属性，叫`__proto__`，这个属性会指向该对象的原型。

那么原型、构造函数和实例对象的关系如下图

![原型对象.jpg](https://gitee.com/Raydaydayup/images/raw/master/原型对象.jpg)

## 原型链

原型本质上也是对象，那么原型是不是也有原型？答案是肯定的。原型是是有原型的。那么按照上面的关系图，我们就可以推测出，一个原型之间的关系。

![原型链.jpg](https://gitee.com/Raydaydayup/images/raw/master/原型链.jpg)

不一定就是两层，可以是多层的关系。那么原型链到底是什么呢？原型链其实就是把上图的构造函数去掉的部分。

前面提到原型对象的属性可被共享的，那么我最下面的实例对象是可以访问到`Person.prototype`上的属性，`Person.prototype`又可以访问到`Object.prototype`上的属性的。所以下面的式子是成立的

```js
person1.a === Person.prototype.a === Object.prototype.a
```

那么去访问一个对象的属性的话，首先在对象本身查找这个属性，如果没有再到原型对象上查找这个属性，还没有再往上查找，一直到`Object.prototype`才停止，这就是**原型链**。

### 手写一下instanceof

**`instanceof`** **运算符**用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上。

我们利用原型链的原理手写一下

```js
function myInstanceOf(obj, constructor) {
  if ((typeof obj !== 'object' && typeof obj !== 'function') || obj === null) {
    return false
  }
  const proto = Object.getPrototypeOf(obj)
  if (proto === constructor.prototype) { // 利用构造函数的
    return true
  } else {
    return myInstanceOf(proto, constructor)
  }
}

console.log(myInstanceOf([], Array)) // true
console.log(myInstanceOf(false, Array)) // true
```

## 函数和构造函数（函数都有``prototype`属性吗？）

先来问一个问题，**函数都有``prototype`属性吗？**

我们来看看ECM-262，构造函数的解释

> function object that creates and initializes objects.
>
> The value of a constructor's "prototype" property is a prototype object that is used to implement inheritance and shared properties.
>
> 翻译：
>
> 创建和初始化对象的函数对象。
>
> 构造函数的“prototype”属性的值是一个原型对象，用于实现继承和共享属性。

好像构造函数就是一个函数，但是有什么区别呢？我们再来看一段原文

> A function object is an object that supports the [[Call]] internal method.
>
> A constructor is an object that supports the [[Construct]] internal method. 
>
> Every object that supports [[Construct]] must support [[Call]]; that is, every constructor must be a function object. 
>
> Therefore, a constructor may also be referred to as a constructor function or constructor function object.

我们来看看这段话：

1. 一个函数对象是一个支持`[[Call]]`内部方法的对象。
2. 一个构造函数是一个支持`[[Construct]]`内部方法的对象。
3. 实现了`[[Construct]]`内部方法的对象一定支持`[[Call]]`内部方法，那么每个构造函数都是函数对象
4. 因此，一个`constructor`也被叫做构造函数或者构造函数对象

那现在很清晰了解了函数和构造函数两者的区别与联系，关键在于是否实现了`[[Construct]]`内部方法。那么`[[Construct]]`是负责干啥的呢？这就不贴原文了，他是通过`new`和`super`的调用创建一个对象。

**那么回到上面的问题，构造函数肯定有``prototype`属性，但其他函数都有这个属性吗？**我们来写段代码测试下

```js
function fn() {}
const arrowFn = () => {}
class classFn {}
function* genFn() {}
async function asyncFn() {}
async function* asyncGenFn() {}

console.log(fn.hasOwnProperty('prototype')) // true
console.log(arrowFn.hasOwnProperty('prototype')) // false
console.log(classFn.hasOwnProperty('prototype')) // true
console.log(genFn.hasOwnProperty('prototype')) // true
console.log(asyncFn.hasOwnProperty('prototype')) // false
console.log(asyncGenFn.hasOwnProperty('prototype')) // true
```

从上述代码的执行结果来看，并不是所有函数都有``prototype`属性的，至于为什么，可定是没有实现`[[Construct]]`内部方法啦（😂）。

对于我们来说只要记住我们这种`function fn() {}`函数是可以当作构造器使用的。

## 

## new到底经历了啥

new到底经历了啥，他是干啥的？来来来，继续看他规范里怎么写

![20200805-103540-0554.png](https://gitee.com/Raydaydayup/images/raw/master/20200805-103540-0554.png)

具体的关于`NewExpression`和`MemberExpression`的定义我并不知道，按照我的理解`NewExpression`应该是`New F`形式的表达式，而`MemberExpression`应该是`New F (args)`

形式的调用。

我们在看下`EvaluateNew`的执行过程和结果（个人理解）：

1. 第1，2步先断言，我的理解就是，确定`constructExpr`是`NewExpression`还是`MemberExpression`，确定参数是空的还是有参数。
2. 第3，4步通过`constructExpr`表达式找到对应的构造函数`constructor`
3. 第5，6步处理参数
4. 第7步判断`constructor`是否为构造函数，不是就报错，通过判断内部是否有`[[Construct]]`实现。
5. 第8步返回`Construct(constructor, argList)`的调用结果

核心就是`Construct(constructor, argList)`这一步了，那他到底是啥？具体也不扒他的规范了，它内部是执行`[[Construct]]`内部方法。看看他是搞啥的

![20200805-104040-0991.png](https://gitee.com/Raydaydayup/images/raw/master/20200805-104040-0991.png)

看到这里我自己都有点懵了，这是个啥？慢慢看看，然后把看不懂的先屏蔽。

1. 先看第5步，这步的结果就是创建一个原型为`F.prototype`的对象，`newTarget`是指向函数`F`的引用
2. 再看第8步，这步简单的理解就是改变`this`指向，其实内部好多关于执行上下文的操作，我也没弄清楚
3. 再看11步，这步简单的理解就是解析构造函数里的代码，获取解析的结果
4. 再看13和14步，`If result.[[Type]] is return`这里，按照我的理解就是判断函数是否提前`return`，也就是自己是否写了`return`，如果不是提前`return`，就继续判断，如果是就直接返回结果。
5. 来看下第13步是干了啥，重要的是13.b这里，`If kind is base`，就返回之前创建的对象。

那么这就符合我们正常的理解：

1. 创建一个空对象，作为将要返回的对象实例。
2. 将这个空对象的原型，指向构造函数的 `prototype` 属性。
3. 将这个空对象赋值给函数内部的 `this` 关键字。
4. 开始执行构造函数内部的代码。

但是这都取决于`If kind is base`这个，那么这个`kind`是啥，看下图中第4步，这又是一个函数的内置的属性`[[ConstructorKind]]`，这个内部属性有两个值：`base | derived`，用来判断是否是派生类构造函数。

到这里我们就知道了，正常的构造函数`new`的大概步骤是上面的形式。但是派生类构造函数就不一样了。

### 手写一下正常构造函数的`new`

知道了流程那么我们手写一下

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

## 关于继承

关于继承相关的知识，可以参考大佬的文章[JavaScript深入之继承的多种方式和优缺点](https://github.com/mqyqingfeng/Blog/issues/16)

## 关于Class做一些小实验

`Class`是一个原型继承的语法糖，这个大家都认同，那`Class`到底是啥？来做几个小实验

```js
class MyClass {
  constructor() {
    this.a = 1
  }
  say() {
    console.log('hi')
  }
}

class MyChildClass extends MyClass {
  constructor() {
    super()
    this.b = 2
  }
  say() {
    console.log('hi Child')
  }
}

const myInstance = new MyClass()
const myChildInstance = new MyChildClass()

console.log(Object.prototype.toString.call(MyClass)) // [object Function]
console.log(MyClass.hasOwnProperty('prototype')) // true

console.log(myInstance) // {a:1}
console.log(Object.getPrototypeOf(myInstance) === MyClass.prototype) // true

MyClass.prototype.say() // hi

console.log(myChildInstance) // { a: 1, b: 2 }
console.log(Object.getPrototypeOf(MyChildClass.prototype) === MyClass.prototype) // true

MyChildClass.prototype.say() // hi Child
Object.getPrototypeOf(MyChildClass.prototype).say() // hi
```

从上面代码可以看出来

1. Class声明出来的本质上是一个函数
2. 这个类的原型其实就是实例的原型
3. 继承就是利用父类的原型创建子类的原型