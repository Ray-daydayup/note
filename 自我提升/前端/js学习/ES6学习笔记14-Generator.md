## Generator

什么是 JavaScript Generators 呢？通俗的讲 Generators 是可以用来控制迭代器的函数。它们可以暂停，然后在任何时候恢复。如果这句话不好理解，可以看下接下来的示例。

1. 常规循环

```js
for (let i = 0; i < 5; i += 1) {
    console.log(i)
}
// this will return immediately 0 -> 1 -> 2 -> 3 -> 4
```
        
2. 利用 Generator

```js
function * generatorForLoop () {
    for (let i = 0; i < 5; i += 1) {
        yield console.log(i)
    }
}
const genForLoop = generatorForLoop()
console.log(genForLoop.next()) // first console.log - 0
console.log(genForLoop.next()) // 1
console.log(genForLoop.next()) // 2
console.log(genForLoop.next()) // 3
console.log(genForLoop.next()) // 4
```
        
对比下代码，常规的循环只能一次遍历完所有值，Generator 可以通过调用 next 方法拿到依次遍历的值，让遍历的执行变得“可控”。

### 语法

调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象（Iterator Object）

下一步，必须调用遍历器对象的next方法，使得指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。换言之，Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。

```JS
function * gen () {
    yield 1
    yield 2
    yield 3
}
let g = gen()
// "Generator { }"
```
        
这个是 Generator 的定义方法，有几个点值得注意：

- 比普通函数多一个 *
- 函数内部用 yield 来控制程序的执行的“暂停”
- 函数的返回值通过调用 next 来“恢复”程序执行
- **Generator 函数的定义不能使用箭头函数，否则会触发 SyntaxError 错误**

```JS
let generator = *() => {} // SyntaxError
let generator = ()* => {} // SyntaxError
let generator = (*) => {} // SyntaxError        
//这些做法都是错误的❌。
```

### yield 表达式

- yield 关键字用来暂停和恢复一个生成器函数

关于 yield 表达式，要熟记几个知识点：

（1）遇到yield表达式，就暂停执行后面的操作，并将紧跟在yield后面的那个表达式的值，作为返回的对象的value属性值。

（2）下一次调用next方法时，再继续往下执行，直到遇到下一个yield表达式。

（3）如果没有再遇到新的yield表达式，就一直运行到函数结束，直到return语句为止，并将return语句后面的表达式的值，作为返回的对象的value属性值。

（4）如果该函数没有return语句，则返回的对象的value属性值为undefined

yield 表达式的返回值是 undefined，但是遍历器对象的 next 方法可以修改这个默认值。

```JS
function * gen () {
    let val
    val = yield 1
    console.log(`1:${val}`) // 1:undefined
    val = yield 2
    console.log(`2:${val}`) // 2:undefined
    val = yield 3
    console.log(`3:${val}`) // 3:undefined
}
var g = gen()
console.log(g.next()) // {value: 1, done: false}
console.log(g.next()) // {value: 2, done: false}
console.log(g.next()) // {value: 3, done: false}
console.log(g.next()) // {value: undefined, done: true}
```
        
从这个代码可以看出来，yield 表达式的返回值是 undefined。

yeild  是委托给另一个遍历器对象或者可遍历对象

```js
function * gen () {
    let val
    val = yield 1
    console.log(`1:${val}`) // 1:undefined
    val = yield 2
    console.log(`2:${val}`) // 2:undefined
    val = yield [3, 4, 5]
    console.log(`3:${val}`) // 3:undefined
}
```
        
### 方法

Generator 对象有几个方法，next、return、throw。

- next([value])
**`next` 方法可以带一个参数，该参数就会被当作上一个 `yield` 表达式的返回值。**
前面我们讲了，Generator 对象通过 next 方法来获取每一次遍历的结果，这个方法返回一个对象，这个对象包含两个属性：value 和 done。value 是指当前程序的运行结果，done 表示遍历是否结束。

其实 next 是可以接受参数的，这个参数可以让你在 Generator 外部给内部传递数据，而这个参数就是作为 yield 的返回值。

```js
function * gen () {
    var val = 100
    while (true) {
        console.log(`before ${val}`)
        val = yield val
        console.log(`return ${val}`)
    }
}
var g = gen()
console.log(g.next(20).value)
// before 100
// 100
console.log(g.next(30).value)
// return 30
// before 30
// 30
console.log(g.next(40).value)
// return 40
// before 40
// 40
```
        
如果对上面的话和代码不理解，可以把 console.log(g.next(30).value) 和 console.log(g.next(40).value)注释掉。你会发现 只输出了 before 100 和 100，这是为什么呢？下面我们来还原下这段代码的执行过程：

g.next(20) 这句代码会执行 gen 内部的代码，遇到第一个 yield 暂停。所以 console.log(`before ${val}`) 执行输出了 before 100，此时的 val 是 100，所以执行到 yield val 返回了 100，注意 yield val 并没有赋值给 val。

g.next(30) 这句代码会继续执行 gen 内部的代码，也就是 val = yield val 这句，因为 next 传入了 30，所以 yield val 这个返回值就是 30，因此 val 被赋值 30，执行到console.log(`return ${val}`)输出了 30，此时没有遇到 yield 代码继续执行，也就是 while 的判断，继续执行console.log(`before ${val}`) 输出了 before 30，再执行遇到了yield val程序暂停。

g.next(40) 重复步骤 2。

```js
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}

var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```

上面代码中，第二次运行next方法的时候不带参数，导致 y 的值等于2 * undefined（即NaN），除以 3 以后还是NaN，因此返回对象的value属性也等于NaN。第三次运行Next方法的时候不带参数，所以z等于undefined，返回对象的value属性等于5 + NaN + undefined，即NaN。

如果向next方法提供参数，返回结果就完全不一样了。上面代码第一次调用b的next方法时，返回x+1的值6；第二次调用next方法，将上一次yield表达式的值设为12，因此y等于24，返回y / 3的值8；第三次调用next方法，将上一次yield表达式的值设为13，因此z等于13，这时x等于5，y等于24，所以return语句的值等于42。

注意，由于next方法的参数表示上一个yield表达式的返回值，**所以在第一次使用next方法时，传递参数是无效的**。V8 引擎直接忽略第一次使用next方法时的参数，只有从第二次使用next方法开始，参数才是有效的。从语义上讲，第一个next方法用来启动遍历器对象，所以不用带有参数。


- return()

return 方法可以让 Generator 遍历终止，有点类似 for 循环的 break。

```js
function * gen () {
          yield 1
          yield 2
          yield 3
        }
var g = gen()
        
console.log(g.next()) // {value: 1, done: false}
console.log(g.return()) // {value: undefined, done: true}
console.log(g.next()) // {value: undefined, done: true}   
```

从 done 可以看出代码执行已经结束。

当然 return 也可以传入参数，作为返回的 value 值。

```js
function * gen () {
    yield 1
    yield 2
    yield 3
}
        
var g = gen()     
console.log(g.next()) // {value: 1, done: false}
console.log(g.return(100)) // {value: 100, done: true}
console.log(g.next()) // {value: undefined, done: true}
```
        
- throw()

可以通过 throw 方法在 Generator 外部控制内部执行的“终断”。

```js
function* gen () {
    while (true) {
        try {
            yield 42
        } catch (e) {
            console.log(e.message)
        }
    }
}
let g = gen()
console.log(g.next()) // { value: 42, done: false }
console.log(g.next()) // { value: 42, done: false }
console.log(g.next()) // { value: 42, done: false }
// 中断操作
g.throw(new Error('break'))
console.log(g.next()) // {value: undefined, done: true}
```
        
如果想退出遍历 catch 之后可以配合 return false 使用，能起到 “break” 的作用

### Scene Practice

Generator 相对抽象些，我们多列举一些应用场景帮助大家理解。

场景1
我们在商品搞活动的时候，通常会有抽奖的环节。回忆下抽奖的流程：

满足1000积分的可以抽一等奖（一名）
满足500积分的可以抽二等奖（三名）
满足50积分的可以抽三等奖（五名）

```js
function * draw (first = 1, second = 3, third = 5) {
          let firstPrize = ['1A', '1B', '1C', '1D', '1E', '1F', '1G', '1H', '1I', '1J', '1K', '1L', '1M', '1N']// 满足1000积分的名单
          let secondPrize = ['2O', '2P', '2Q', '2R', '2S', '2T', '2U', '2V', '2W', '2X', '2Y', '2Z'] // 满足500积分的名单
          let thirdPrize = ['31', '32', '33', '34', '35', '36', '37', '38', '39'] // 满足50积分的名单
          let count = 0
          let random
          while (1) {
            if (count < first) {
              random = Math.floor(Math.random() * firstPrize.length)
              yield firstPrize[random]
              count++
              firstPrize.splice(random, 1)
            } else if (count < first + second) {
              random = Math.floor(Math.random() * secondPrize.length)
              yield secondPrize[random]
              count++
              secondPrize.splice(random, 1)
            } else if (count < first + second + third) {
              random = Math.floor(Math.random() * thirdPrize.length)
              yield thirdPrize[random]
              count++
              thirdPrize.splice(random, 1)
            } else {
              return false
            }
          }
        }
        let t = draw()
        console.log(t.next().value)
        console.log(t.next().value)
        console.log(t.next().value)
        console.log(t.next().value)
        console.log(t.next().value)
        console.log(t.next().value)
        console.log(t.next().value)
        console.log(t.next().value)
        console.log(t.next().value)
```
        
从这个代码可以看出来，每次执行都是临时 random，如果是普通函数就只能用三个循环，依次 random 所有名单，这个函数返回所有中奖的名单，而不是每次返回单个名单。

```js
function draw (first = 1, second = 3, third = 5) {
          let firstPrize = ['1A', '1B', '1C', '1D', '1E', '1F', '1G', '1H', '1I', '1J', '1K', '1L', '1M', '1N']// 满足1000积分的名单
          let secondPrize = ['2O', '2P', '2Q', '2R', '2S', '2T', '2U', '2V', '2W', '2X', '2Y', '2Z'] // 满足500积分的名单
          let thirdPrize = ['31', '32', '33', '34', '35', '36', '37', '38', '39'] // 满足50积分的名单
          let result = []
          let random
          for (let i = 0; i < first; i++) {
            random = Math.floor(Math.random() * firstPrize.length)
            result = result.concat(firstPrize.splice(random, 1))
          }
          for (let i = 0; i < second; i++) {
            random = Math.floor(Math.random() * secondPrize.length)
            result = result.concat(secondPrize.splice(random, 1))
          }
          for (let i = 0; i < third; i++) {
            random = Math.floor(Math.random() * thirdPrize.length)
            result = result.concat(thirdPrize.splice(random, 1))
          }
          return result
        }
let t = draw()
        for (let value of t) {
          console.log(value)
        }
```
        
大家对比下普通函数和 Generator 的两种写法，哪个体验更好呢？我们总结一下：

如果用普通函数的来写，主持人说现在获得一等奖的名单时，其实二等奖和三等奖已经确定了，如果观众知道内幕是不是要不开心了。

如果用Generator来写，主持人每宣布一个名单时，下一个的中奖人他也不知道，这才是真正意义的抽奖体验吧。

- 场景2

我们经常玩一些小游戏，比如数数字，遇到 3 的倍数就要跳过，从 1 一直往下数：

```js
function * count (x = 1) {
          while (1) {
            if (x % 3 !== 0) {
              yield x
            }
            x++
          }
        }
let num = count()
console.log(num.next().value)
console.log(num.next().value)
console.log(num.next().value)
console.log(num.next().value)
console.log(num.next().value)
```
        
通过 Generator 我们就能轻松实现，只要调用 num.next 我们就知道下一个数是什么了，而使用普通函数却没法做到。不信你试试看？

练习
用 Generator 实现一个斐波那契数列？
用 Generator 给自定义数据结构写一个遍历器？
阅读
<ol>
        <li><a href="https://codeburst.io/what-are-javascript-generators-and-how-to-use-them-c6f2713fd12e">What are JavaScript Generators and how to use them</a></li>
        <li><a href="https://davidwalsh.name/es6-generators">The Basics Of ES6 Generators</a></li>
        <li><a href="https://davidtang.io/2016/10/15/a-practical-introduction-to-es6-generator-functions.html">A Practical Introduction to ES6 Generator Functions</a></li>
        <li><a href="https://baike.baidu.com/item/%E6%96%90%E6%B3%A2%E9%82%A3%E5%A5%91%E6%95%B0%E5%88%97">斐波那契数列</a></li>
        <li><a href="https://exploringjs.com/es6/ch_generators.html">Generators</a></li>
</ol>