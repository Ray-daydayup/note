### ES5回调地狱 Callbacks

- `JavaScipt` 中的许多操作都是异步的，比如我们想加载一个脚本资源，可以把这个操作封装在一个 `loadScript(src)` 函数：
- 这个函数的作用是加载一个新的脚本。当使用 `<script src="…">` 将其添加到文档中时，浏览器就会对它进行加载和执行。
```js
function loadScript (src) {
    let script = document.createElement('script')
    script.src = src
    document.head.append(script)
}
// 加载并执行脚本
loadScript('/my/script.js');
```

- 函数是异步调用的，因为操作不是此刻（加载脚本）完成的，而是之后。调用初始化脚本加载，然后继续执行。当脚本正在被加载时，下面的代码可能已经完成了执行，如果加载需要时间，那么同一时间，其他脚本可能也会被运行。

```js
loadScript('/my/script.js');
// 下面的代码在加载脚本时，不会等待脚本被加载完成
// ...
```
        
-  现在，我们假设想在新脚本被加载完成时，被立即使用。它可能声明了新函数，因此我们想要运行它们。但如果我们在 loadScript(…) 调用后，立即那么做，就会导致操作失败。

```js
loadScript('/my/script.js'); // 脚本含有 "function newFunction() {…}"
newFunction(); // 没有这个函数！
```
        
- 很明显，浏览器没有时间去加载脚本。因此，对新函数的立即调用失败了。`loadScript` 函数并没有提供追踪加载完成时方法。脚本加载然后最终的运行，仅此而已。但我们希望了解脚本何时加载完成，以使用其中的新函数和新变量。

- 我们将 `callback` 函数作为第二个参数添加至 `loadScript` 中，函数在脚本加载时被执行：

```js
function loadScript (src, callback = () => console.log("noCallback")) {//callback默认值
    let script = document.createElement('script')
    script.src = src
    script.onload = () => callback(script)//script传参给回调函数（闭包）
    document.head.append(script)
}
```
        
- 如果现在你想从脚本中调用新函数，我们应该在回调函数中那么写：

```js
loadScript('/my/script.js', function() {
          // 在脚本被加载后，回调才会被运行
          newFunction(); // 现在起作用了
          ...
});
```
        
这个过程大家并不陌生，可是如果在回调之后再回调呢？

```js
loadScript('/my/script.js', function (script) {
    alert(`Cool, the ${script.src} is loaded, let's load one more`)
    loadScript('/my/script2.js', function (script) {
        alert(`Cool, the second script is loaded`)
    })
})
//或
loadScript('/my/script.js', function (script) {
    loadScript('/my/script2.js', function (script) {
        loadScript('/my/script3.js', function (script) {
              // ...在所有脚本被加载后继续操作
        })
    })
})

```
     
- 这两个小的场景都是在描述按顺序加载不同的脚本，采用了回调之后再回调的连锁过程。可以看出，这样的代码嵌套层级多了之后就是噩梦，还好我们上述只考虑了正常的情况，如果再把异常考虑进来代码大概是这个样子：

```js
loadScript('1.js', function (error, script) {
          if (error) {
            handleError(error)
          } else {
            // ...
            loadScript('2.js', function (error, script) {
              if (error) {
                handleError(error)
              } else {
                // ...
                loadScript('3.js', function (error, script) {
                  if (error) {
                    handleError(error)
                  } else {
                    // ...加载所有脚本后继续 (*)
                  }
                })
              }
            })
          }
        })
```

- 我们加载 `1.js`，如果没有发生错误；
- 我们加载 `2.js`，如果没有发生错误；
- 我们加载 `3.js`，如果没有发生错误 —— 做其他操作。
- 如果嵌套变多，代码层次就会变深，维护难度也随之增加，尤其是如果我们有一个不是 … 的真实代码，就会包含更多的循环，条件语句等。
- 这有时称为 “回调地狱” 或者“回调金字塔”。

### Promise Syntax

首先要说明 `Promise` 就是为了解决“回调地狱”问题的，它可以将异步操作的处理变得很优雅。如果我们使用 `Promise` 来解决上面那个问题该怎么做呢？

```js
function loadScript (src) {
          return new Promise((resolve, reject) => {
            let script = document.createElement('script')
            script.src = src
            script.onload = () => resolve(script)
            script.onerror = (err) => reject(err)
            document.head.append(script)
          })
        }
        
loadScript('1.js').then(loadScript('2.js'), (err) => {
            console.log(err)
          })
          .then(loadScript('3.js'), (err) => {
            console.log(err)
          })
```
        
我们先来看下 `Promise` 的基本用法，并思考有哪些应用场景可以发挥它的长处。

- 基本语法 `new Promise( function(resolve, reject) {…} );`

- 通过创建 `Promise` 对象开启一个异步操作的过程，一般用几步完成多次异步操作：
    1. `new Promise(fn)` 返回一个 `Promise` 对象
    2. 在 `fn` 中指定异步等处理
    3. 处理结果正常的话，调用 `resolve` (处理结果值)
    4. 处理结果错误的话，调用 `reject(Error对象)`
    5. 我们根据这个语法尝试自己封装一个 `Ajax` 的方法：

```js
function getURL (URL) {
    return new Promise(function (resolve, reject) {
        var req = new XMLHttpRequest()
        req.open('GET', URL, true)
        req.onload = function () {
            if (req.status === 200) {
                resolve(req.responseText)
            } else {
                reject(new Error(req.statusText))
            }
        }
        req.onerror = function () {
            reject(new Error(req.statusText))
        }
        req.send()
})
}
// 运行示例
var URL = 'http://httpbin.org/get'
getURL(URL).then(function onFulfilled (value) {
    console.log(value)
}).catch(function onRejected (error) {
    console.error(error)
})
```
        
- `getURL` 只有在通过 `XHR` 取得结果状态为 `200` 时才会调用 `resolve` ，也就是只有数据取得成功时，而其他情况（取得失败）时则会调用 `reject` 方法。
- `resolve(req.responseText)` 在`response`的内容中加入了参数。 `resolve`方法的参数并没有特别的规则，基本上把要传给回调函数参数放进去就可以了。 ( `then` 方法可以接收到这个参数值)
- 在这里必须说明下 `Promise` 内部是有状态的`(pending、fulfilled、rejected)`，`Promise` 对象根据状态来确定执行哪个方法。`Promise` 在实例化的时候状态是默认 `pending` 的，当异步操作是完成的，状态会被修改为 `fulfilled`，如果异步操作遇到异常，状态会被修改为 `rejected`
- 状态转化是单向的，不可逆转，已经确定的状态`（fulfilled/rejected）`无法转回初始状态（`pending`），而且只能是从 `pending` 到 `fulfilled` 或者 `rejected`
#### 方法

1. `Promise.prototype.then()`
- 基本语法 `promise.then(onFulfilled, onRejected);`

```js
var promise = new Promise(function (resolve, reject) {
          resolve('传递给then的值')
        })
        promise.then(function (value) {
          console.log(value)
        }, function (error) {
          console.error(error)
        })
```
        
- 这段代码创建一个 `Promise` 对象，定义了处理 `onFulfilled` 和 `onRejected` 的函数（`handler`），然后返回这个 `Promise` 对象。
- 这个 `Promise` 对象会在变为 `resolve` 或者 `reject` 的时候分别调用相应注册的回调函数。
- 当 `handler` 返回一个正常值的时候，这个值会传递给 `Promise` 对象的 `onFulfilled` 方法。
- 定义的 `handler` 中产生异常的时候，这个值则会传递给 `Promise` 对象的 `onRejected` 方法。
- `Promise.resolve()`
    - 一般情况下我们都会使用 `new Promise()` 来创建 `Promise` 对象，但是除此之外我们也可以使用其他方法。在这里，我们将会学习如何使用 `Promise.resolve` 和 `Promise.reject` 这两个方法。
    - 静态方法 `Promise.resolve(value)` 可以认为是 `new Promise()` 方法的快捷方式。
    - 比如 `Promise.resolve(42)` 可以认为是以下代码的语法糖。
    - 在这段代码中的 `resolve(42)` 会让这个 `Promise` 对象立即进入确定（即`resolved`）状态，并将 42 传递给后面 `then` 里所指定的 `onFulfilled` 函数。
    - 方法 `Promise.resolve(value`) 的返回值也是一个 `Promise` 对象，所以我们可以像下面那样接着对其返回值进行 `.then` 调用。
    - `Promise.resolve` 作为 `new Promise()` 的快捷方式，在进行 `Promise` 对象的初始化或者编写测试代码的时候都非常方便。

```js
new Promise(function (resolve) {
          resolve(42)
        })
Promise.resolve(42).then(function (value) {
          console.log(value)
        })
```
        
- `Promise.reject()`
    - `Promise.reject(error)` 是和 `Promise.resolve(value) `类似的静态方法，是 `new Promise()` 方法的快捷方式。
    - 这段代码的功能是调用该 `Promise` 对象通过 `then` 指定的 `onRejected` 函数，并将错误（`Error`）对象传递给这个 `onRejected` 函数。
    - 比如 `Promise.reject(new Error(“出错了”))` 就是下面代码的语法糖形式。

```js
new Promise(function (resolve, reject) {
          reject(new Error('出错了'))
        })
Promise.reject(new Error('BOOM!'))
```
        
2. `Promise.prototype.catch()`

- 捕获异常是程序质量保障最基本的要求，可以使用 `Promise` 对象的 `catch` 方法来捕获异步操作过程中出现的任何异常。

- 基本语法 `p.catch(onRejected)`

```js
p.catch(function(reason) {
// rejection
});

function test () {
          return new Promise((resolve, reject) => {
            reject(new Error('es'))
          })
        }
test().catch((e) => {
          console.log(e.message) // es
        })
```

- 这个代码展示了如何使用 `catch` 捕获 `Promise` 对象中的异常，有的同学会问 `catch` 捕获的是 `Promise` 内部的 `Error` 还是 `Reject`？上面的示例既用了 `reject` 也用了 `Error`，到底是哪个触发的这个捕获呢？

```js
function test () {
          return new Promise((resolve, reject) => {
            throw new Error('wrong')
          })
        }
test().catch((e) => {
          console.log(e.message) // wrong
        })
```
        
- 这个代码对比着上个代码就能明显感受出来的，`throw Error` 和 `reject` 都触发了 `catch` 的捕获，而第一个用法中虽然也有 `Error` 但是它不是 `throw`，只是 `reject` 的参数是 `Error` 对象，换句话说 `new Error` 不会触发 `catch`，而是 `reject`。

- **不建议在 `Promise` 内部使用 `throw` 来触发异常，而是使用 `reject(new Error())` 的方式来做，因为 `throw` 的方式并没有改变 `Promise` 的状态**

3. `Promise.all()`

- 基本语法 `Promise.all(promiseArray)`;

示例

```js
var p1 = Promise.resolve(1)
var p2 = Promise.resolve(2)
var p3 = Promise.resolve(3)
Promise.all([p1, p2, p3]).then(function (results) {
    console.log(results) // [1, 2, 3]
})
```
        
- `Promise.all` 生成并返回一个新的 `Promise` 对象，所以它可以使用 `Promise` 实例的所有方法。参数传递 `promise` 数组中所有的 `Promise` 对象都变为 `resolve` 的时候，该方法才会返回， 新创建的 `Promise` 则会使用这些 `promise` 的值。
- 如果参数中的任何一个 `promise` 为 `reject` 的话，则整个 `Promise.all` 调用会立即终止，并返回一个 `reject` 的新的 `Promise` 对象。
- 由于参数数组中的每个元素都是由 `Promise.resolve` 包装（`wrap`）的，所以`Paomise.all` 可以处理不同类型的 `promise` 对象。

4. `Promise.race()`

- 基本语法 `Promise.race(promiseArray)`;

示例

```js
var p1 = Promise.resolve(1)
var p2 = Promise.resolve(2)
var p3 = Promise.resolve(3)
Promise.race([p1, p2, p3]).then(function (value) {
    console.log(value) // 1
})
```
        
- `Promise.race` 生成并返回一个新的 `Promise` 对象。
- 参数 `promise` 数组中的任何一个 `Promise` 对象如果变为 `resolve` 或者 `reject` 的话， 该函数就会返回，并使用这个 `Promise` 对象的值进行 `resolve` 或者 `reject`

**练习**
按顺序加载脚本的代码不太直观看出顺序执行的过程，如果按顺序延时执行是不是更好理解呢？
```js
const fn1 = () => {
          return new Promise((resolve, reject) => {
            setTimeout(() => {
              console.log(111)
              resolve()
            }, 100)
          })
        }
const fn2 = () => {
          return new Promise((resolve, reject) => {
            setTimeout(() => {
              console.log(222)
              resolve()
            }, 300)
          })
        }
const fn3 = () => {
          return new Promise((resolve, reject) => {
            setTimeout(() => {
              console.log(333)
              resolve()
            }, 200)
          })
        }
const fn4 = () => {
          return new Promise((resolve, reject) => {
            setTimeout(() => {
              console.log(444)
              resolve()
            }, 50)
          })
        }
// 顺序执行函数
fn1()
    .then(fn2)
    .then(fn3)
    .then(fn4)
```
        
        
请用 Promise 实现一个接口。
**阅读**

<ol>
        <li><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise">Promise</a></li>
        <li><a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch">fetch</a></li>
        <li><a href="https://developers.google.com/web/fundamentals/primers/promises?hl=zh-cn">JavaScript Promise</a></li>
        <li><a href="https://ponyfoo.com/articles/es6-promises-in-depth">ES6 Promises in Depth</a></li>
        <li><a href="http://ccoenraets.github.io/es6-tutorial/promises/">Using Promises</a></li>
        <li><a href="https://scotch.io/tutorials/javascript-promises-for-dummies">JavaScript Promises for Dummies</a></li>
</ol>
