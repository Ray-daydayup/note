## Proxy

- 在 ES6 标准中新增的一个非常强大的功能是 `Proxy`，它可以自定义一些常用行为如查找、赋值、枚举、函数调用等。通过 `Proxy` 这个名称也可以看出来它包含了“代理”的含义，只要有“代理”的诉求都可以考虑使用 `Proxy` 来实现。
- 语法 `let p = new Proxy(target, handler)`
  
|  参数  |                                     含义                                     | 必选 |
| ------ | ---------------------------------------------------------------------------- | --- |
| target | 用 Proxy 包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理） | Y    |
| hander | 一个对象，其属性是当执行一个操作时定义代理的行为的函数                            | Y    |

- MDN 给出的解释偏官方，通俗的讲第一个参数 `target` 就是用来代理的“对象”，被代理之后它是不能直接被访问的，而 `handler` 就是实现代理的过程。

```js
//我们经常读取一个对象的 key-value：
let o = {
    name: 'xiaoming',
    age: 20
}
console.log(o.name) // xiaoming
console.log(o.age) // 20
console.log(o.from) // undefined
```
        
- 当我们读取 `from` 的时候返回的是 `undefined`，因为 `o` 这个对象中没有这个 `key-value`。想想看我们在读取数据的时候，这个数据经常是聚合的，当大家没有按照规范来的时候或者数据缺失的情况下，经常会出现这种“乌龙”现象。
- 如果我们不想在调用 `key` 的时候返回 `undefined`，之前的做法是这样的：

```js
console.log(o.from || '')
```
        
- 如果我们对所有代码都是这种写法，那阅读性和观赏性就不得而知了。值得庆幸的是，ES6 的 `Proxy` 可以让我们轻松的解决这一问题：

```js
let o = {
    name: 'xiaoming',
    age: 20
}       
let handler = {
    get(obj, key) {
        return Reflect.has(obj, key) ? obj[key] : ''
    }
}        
let p = new Proxy(o, handler)
console.log(p.from)
```
        
- 这个代码是想表达如果 `o` 对象有这个 `key-value` 则直接返回，如果没有一律返回 `''`，当然这里是自定义，大家可以根据自己的需要来写适合自己业务的规则。

### Schema Validation

- 刚才对数据的“读操作”进行了拦截，接下来我们描述下“写操作”进行拦截。

1. 场景 1

- 从服务端获取的数据希望是只读，不允许在任何一个环节被修改。

```js
// response.data 是 JSON 格式的数据，来自服务端的响应
// 在 ES5 中只能通过遍历把所有的属性设置为只读
for (let [key] of Object.entries(response.data)) {
    Object.defineProperty(response.data, key, {
        writable: false
    })
}

//如果我们使用 Proxy 就简单很多了：
let data = new Proxy(response.data, {
    set(obj, key, value) {
        return false
    }
})
```

2. 场景 2

- 对于数据交互而言，校验是不可或缺的一个环境，传统的做法是将校验写在了业务逻辑里，导致代码耦合度较高。如果大家使用 `Proxy` 就可以将代码设计的非常灵活。

```js
// Validator.js
export default (obj, key, value) => {
    if (Reflect.has(key) && value > 20) {
        obj[key] = value
    }
}

import Validator from './Validator'
let data = new Proxy(response.data, {
    set: Validator
})
```

3. 场景 3

- 如果对读写进行监控，可以这样写：

```js
let validator = {
    set(target, key, value) {
        if (key === 'age') {
            if (typeof value !== 'number' || Number.isNaN(value)) {
                throw new TypeError('Age must be a number')
            }
            if (value <= 0) {
                throw new TypeError('Age must be a positive number')
            }
        }
        return true
    }
}
const person = { age: 27 }
const proxy = new Proxy(person, validator)
proxy.age = 'foo'
// <- TypeError: Age must be a number
proxy.age = NaN
// <- TypeError: Age must be a number
proxy.age = 0
// <- TypeError: Age must be a positive number
proxy.age = 28
console.log(person.age)
// <- 28
        
// 添加监控
window.addEventListener(
          'error',
          e => {
            console.log(e.message) // Uncaught TypeError: Age must be a number
          },
          true
        )
```
        
4. 场景 4

- 实例一个对象，每个对象都有一个自己的 id 而且只读。//**不对劲**

```js
class Component {
    constructor () {
        this.proxy = new Proxy({
              id: Math.random().toString(36).slice(-8)
        },{})
    }
    get id () {
        return this.proxy.id
    }
}
```
        
**练习**

- 封装 ajax，让返回的数据是只读的

### Revocable Proxies

- 除了使用常规的代理，还可以创建临时的代理，这个临时可以理解为：代理可以取消。

```js
const dinosaur = {
    name: 'Fred',
    isDangerous: true
}
const dinoProxy = Proxy.revocable(dinosaur, {
    get: (target, property) => {
        return 'RAWRRRRRRRR'
    }
})
// We need this assignment because the `revocable` method returns an object with the proxified object and the revoke method
const proxifiedDinosaur = dinoProxy.proxy
// Now everytime we access a property in our `proxifiedDinosaur` it gets intercepted by the `get` trap in our handler
console.log(proxifiedDinosaur.name) // "RAWRRRRRRRR"
console.log(proxifiedDinosaur.isDangerous) // "RAWRRRRRRRR"
// Now, if we call `revoke` on our `dinoProxy` we get a TypeError when accessing properties on `proxifiedDinosaur`
dinoProxy.revoke()
console.log(proxifiedDinosaur.name) // TypeError: Cannot perform 'get' on a proxy that has been revoked
console.log(proxifiedDinosaur.isDangerous) // TypeError: Cannot perform 'get' on a proxy that has been revoked
```
        
- **一旦 `revoke` 被调用，`proxy` 就失效了，这也就起到了“临时”代理的作用**

**练习**

- 希望在各个组件初始化的时候都赋值一个随机的 ID，但是这个组件调用这个 ID 是固定的，该怎么做？
- 临时代理有哪些应用场景呢？
-  如何把接口的数据用代理进行包装？

**阅读：**
<ol>
        <li><a href="https://ponyfoo.com/articles/es6-proxies-in-depth">ES6 Proxies in Depth</a></li>
        <li><a href="https://lucasfcosta.com/2016/11/15/Meta-Programming-in-JavaScript-Part-Three.html">Meta Programming In JavaScript - Part Three: Proxies and Reflection</a></li>
        <li><a href="http://dealwithjs.io/es6-features-10-use-cases-for-proxy/">10 Use Cases for Proxy</a></li>
        <li><a href="https://www.sitepoint.com/es6-proxies/">How to Use Proxies</a></li>
        <li><a href="https://habr.com/en/post/448214/">ES6 Proxies in practice</a></li>
</ol>