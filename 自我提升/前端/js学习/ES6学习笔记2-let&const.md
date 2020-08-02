## let和const

### let
1. `let` 声明的变量拥有块级作用域

```js
{
    let a = 1
}
console.log(a); //undefined
```
        
`a`变量是在代码块`{}`中使用 `let` 定义的，它的作用域是这个代码块内部，外部无法访问。

2. `let `声明的全局变量不是全局对象的属性
这就意味着:不可以通过 `window.变量名` 的方式访问这些变量，而 `var` 声明的全局变量是`window`的属性，是可以通过 `window.变量名` 的方式访问的。

```js
var a = 1
console.log(window.a); //1
        
let a = 1
console.log(window.a); // undefined
```
        
3. 用`let`重定义变量会抛出一个语法错误
这个很容易理解，使用 var 可以重复定义，使用 let 却不可以。

```js
var a = 1
 var a = 2
console.log(a)  //2

let a = 1
let a = 2
// VM131:1 Uncaught SyntaxError: Identifier 'a' has already been declared
```
 
4. `let`声明的变量不会进行变量提升

```js
function test () {
    console.log(a)
    var a = 1
}
test() //undefined
```
        
上述代码中，`a` 的调用在声明之前，所以它的值是 `undefined`，而不是 `Uncaught ReferenceError`。实际上因为` var` 会导致变量提升，上述代码和下面的代码等同：

```js
function test () {
    var a
    console.log(a)
    a = 1
}
test() //undefined
```
        
而对于`let`而言，变量的调用是不能先于声明的，看如下代码：

```js
function test () {
    console.log(a)
    let a = 1
}
test()    // Uncaught ReferenceError: Cannot access 'a' before initialization
```
        
在这个代码中，`a` 的调用是在声明之前，因为 `let` 没有发生变量提升，所有读取 `a` 的时候，并没有找到，而在调用之后才找到 `let` 对 `a` 的定义，所以按照 `tc39`的定义会报错。

### const

`const`除了具有`let`的**块级作用域和不会变量提升**外，还有就是它定义的是**常量**，在用`const`定义变量后，我们就不能修改它了，对变量的修改会抛出异常。

```js
const PI = 3.1415;
console.log(PI);
PI = 22;
console.log(PI);  // Uncaught TypeError: Assignment to constant variable.
```
        
这个代码块中因为对 PI 尝试修改，导致浏览器报错，这就说明 const 定义的变量是不能被修改的，它是只读的。

```js
const PI
PI = 3.1415
// Uncaught SyntaxError: Missing initializer in const declaration
```
        
**`const `声明的变量必须进行初始化，不然会抛出异常 `Uncaught SyntaxError: Missing initializer in const declaration`。**

<ol>
    <li><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management">内存管理</a></li>
</ol>