## Reflect

- `Reflect` 是一个内置的对象，它提供拦截 `JavaScript` 操作的方法，这些方法与处理器对象的方法相同。 `Reflect` 不是一个函数对象，因此它是不可构造的。
- 与大多数全局对象不同， `Reflect` 没有构造函数。你不能将其与一个 `new` 运算符一起使用，或者将 `Reflect` 对象作为一个函数来调用。 `Reflect` 的所有属性和方法都是静态的（就像Math对象）

###  Reflect.apply()

- 语法：`Reflect.apply(target, thisArgument, argumentsList)`

|      参数       |                           含义                            | 必选 |
| -------------- | --------------------------------------------------------- | --- |
| target         | 目标函数                                                   | Y    |
| thisArgument   | target函数调用时绑定的this对象	                             | N    |
| argumentsList	 | target函数调用时传入的实参列表，该参数应该是一个类数组的对象	 | N    |

```js
Reflect.apply(Math.floor, undefined, [1.75]) // 1;
Reflect.apply(String.fromCharCode, undefined, [104, 101, 108, 108, 111]) // "hello"
Reflect.apply(RegExp.prototype.exec, /ab/, ['confabulation']).index     // 4
Reflect.apply(''.charAt, 'ponies', [3])    // "i"
```
        
- **ES5 对比**

- 该方法与ES5中 `Function.prototype.apply()`方法类似：调用一个方法并且显式地指定 `this` 变量和参数列表(arguments) ，参数列表可以是数组，或类似数组的对象。

```js
Function.prototype.apply.call(Math.floor, undefined, [1.75]);
```
        
### Reflect.construct()

- `Reflect.construct()` 方法的行为有点像 `new` 操作符 构造函数 ， 相当于运行 `new target(…args)`.
- 语法 `Reflect.construct(target, argumentsList[, newTarget])`

|      参数       |                                       含义                                        | 必选 |
| -------------- | --------------------------------------------------------------------------------- | --- |
| target         | 被运行的目标函数                                                                   | Y    |
| argumentsList	 | 调用构造函数的数组或者伪数组	                                                     | Y   |
| newTarget	     | 该参数为构造函数， 参考 new.target 操作符，如果没有newTarget参数， 默认和target一样	 | N    |

- 如果`target`或者`newTarget`不是构造函数，抛出`TypeError`
- `Reflect.construct`允许你使用可变的参数来调用构造函数

```js
var obj = new Foo(...args);
var obj = Reflect.construct(Foo, args);
```

```js
var d = Reflect.construct(Date, [1776, 6, 4])
        d instanceof Date // true
        d.getFullYear() // 1776
```
        
如果使用 `newTarget` 参数，则表示继承了 `newTarget` 这个超类：

```js
function someConstructor () {}
        var result = Reflect.construct(Array, [], someConstructor)
Reflect.getPrototypeOf(result) // 输出：someConstructor.prototype
        Array.isArray(result) // true
```
        
### Reflect.define​Property()

- 静态方法 `Reflect.defineProperty()` 基本等同于 `Object.defineProperty()` 方法，唯一不同是返回 `Boolean` 值。
- 语法 `Reflect.defineProperty(target, propertyKey, attributes)`

|    参数     |          参数          | 参数 |
| ----------- | ---------------------- | --- |
| target      | 目标对象                | Y    |
| propertyKey | 要定义或修改的属性的名称 | Y    |
| attributes  | 要定义或修改的属性的描述 | Y    |

```js
const student = {}
Reflect.defineProperty(student, 'name', { value: 'Mike' }) // true
student.name // "Mike"
```
        
### Reflect.delete​Property()

- `Reflect.deleteProperty` 允许你删除一个对象上的属性。返回一个 `Boolean` 值表示该属性是否被成功删除。它几乎与非严格的 `delete operator` 相同。
- 语法 `Reflect.deleteProperty(target, propertyKey)`

|    参数     |        参数         | 参数 |
| ----------- | ------------------ | --- |
| target      | 删除属性的目标对象   | Y    |
| propertyKey | 将被删除的属性的名称 | Y    |

```js
var obj = { x: 1, y: 2 };
Reflect.deleteProperty(obj, "x"); // true
obj; // { y: 2 }
var arr = [1, 2, 3, 4, 5];
Reflect.deleteProperty(arr, "3"); // true
arr; // [1, 2, 3, , 5]
// 如果属性不存在，返回 true
Reflect.deleteProperty({}, "foo"); // true
// 如果属性不可配置，返回 false
Reflect.deleteProperty(Object.freeze({foo: 1}), "foo"); // false
```
        
### Reflect.get()

- `Reflect.get()` 方法的工作方式，就像从 `object (target[propertyKey])` 中获取属性，但它是作为一个函数执行的。
- 语法 `Reflect.get(target, propertyKey[, receiver])`

|    参数     |                参数                 | 参数 |
| ----------- | ---------------------------------- | --- |
| target      | 需要取值的目标对象                   | Y    |
| propertyKey | 需要获取的值的键值                   | Y    |
| receiver    | 如果遇到 getter，此值将提供给目标调用 | N    |

```js
// Object
var obj = { x: 1, y: 2 }
Reflect.get(obj, 'x') // 1
// Array
Reflect.get(['zero', 'one'], 1) // "one"
// Proxy with a get handler
var x = { p: 1 }
var obj = new Proxy(x, {
    get (t, k, r) { return k + 'bar' }
})
Reflect.get(obj, 'foo') // "foobar"  
```
### Reflect.get​OwnProperty​Descriptor()

- 静态方法 `Reflect.getOwnPropertyDescriptor()` 与 `Object.getOwnPropertyDescriptor()` 方法相似。如果在对象中存在，则返回给定的属性的属性描述符，否则返回 `undefined`。
- 语法 `Reflect.getOwnPropertyDescriptor(target, propertyKey)`

|    参数     |             参数              | 参数 |
| ----------- | ----------------------------- | --- |
| target      | 获取自己的属性描述符的属性的名称 | Y    |
| propertyKey | 获取自己的属性描述符的属性的名称 | N    |

```js
Reflect.getOwnPropertyDescriptor({ x: 'hello' }, 'x')
        // {value: "hello", writable: true, enumerable: true, configurable: true}
Reflect.getOwnPropertyDescriptor({ x: 'hello' }, 'y')
        // undefined
Reflect.getOwnPropertyDescriptor([], 'length')
        // {value: 0, writable: true, enumerable: false, configurable: false}
```
   
- 如果该方法的第一个参数不是一个对象（一个原始值），那么将造成 `TypeError` 错误。而对于 `Object.getOwnPropertyDescriptor`，非对象的第一个参数将被强制转换为一个对象处理。

```js
Reflect.getOwnPropertyDescriptor("foo", 0);
        // TypeError: "foo" is not non-null object
Object.getOwnPropertyDescriptor("foo", 0);
        // { value: "f", writable: false, enumerable: true, configurable: false }
```
        
### Reflect.get​PrototypeOf()

- 静态方法 `Reflect.getPrototypeOf()` 与 `Object.getPrototypeOf()` 方法是一样的。都是返回指定对象的原型（即，内部的`[[Prototype]]`属性的值）。
- `Reflect.getPrototypeOf(target)`

|  参数  |       参数        | 参数 |
| ------ | ---------------- | --- |
| target | 获取原型的目标对象 | Y    |

### Reflect.has()

- `Reflect.has`用于检查一个对象是否拥有某个属性， 相当于`in` 操作符
- 语法 `Reflect.has(target, propertyKey)`

|    参数     |                参数                 | 参数 |
| ----------- | ----------------------------------- | --- |
| target      | 目标对象                            | Y    |
| propertyKey | 属性名，需要检查目标对象是否存在此属性 | Y    |

### Reflect.isExtensible()

- `Reflect.isExtensible` 判断一个对象是否可扩展 （即是否能够添加新的属性），它与 `Object.isExtensible()` 方法一样。

- 语法 `Reflect.isExtensible(target)`

|  参数  |   参数   | 参数 |
| ------ | ------- | --- |
| target | 目标对象 | Y    |

### Reflect.own​Keys()

- `Reflect.ownKeys`方法返回一个由目标对象自身的属性键组成的数组。它的返回值等同于 `Object.getOwnPropertyNames(target).concat(Object.getOwnPropertySymbols(target))`

- 语法 `Reflect.ownKeys(target)`

|  参数  |   参数   | 参数 |
| ------ | ------- | --- |
| target | 目标对象 | Y    |

```js
Reflect.ownKeys({z: 3, y: 2, x: 1}); // [ "z", "y", "x" ]
Reflect.ownKeys([]); // ["length"]
var sym = Symbol.for("comet");
var sym2 = Symbol.for("meteor");
var obj = {[sym]: 0, "str": 0, "773": 0, "0": 0,[sym2]: 0, "-1": 0, "8": 0, "second str": 0};
Reflect.ownKeys(obj);
        // [ "0", "8", "773", "str", "-1", "second str", Symbol(comet), Symbol(meteor) ]
        // Indexes in numeric order,
        // strings in insertion order,
        // symbols in insertion order
```
        
### Reflect.prevent​Extensions()

- `Reflect.preventExtensions` 方法阻止新属性添加到对象 例如：防止将来对对象的扩展被添加到对象中)。该方法与 `Object.preventExtensions()` 方法一致
- 语法 `Reflect.preventExtensions(target)`

|  参数  |   参数   | 参数 |
| ------ | ------- | --- |
| target | 目标对象 | Y    |

```js
// Objects are extensible by default.
        var empty = {};
        Reflect.isExtensible(empty); // === true
// ...but that can be changed.
        Reflect.preventExtensions(empty);
        Reflect.isExtensible(empty); // === false
        
Reflect.preventExtensions(1);
        // TypeError: 1 is not an object
Object.preventExtensions(1);
        // 1
```
        
### Reflect.set()

- `Reflect.set`方法允许你在对象上设置属性。它的作用是给属性赋值并且就像 property accessor 语法一样，但是它是以函数的方式。
- 语法 `Reflect.set(target, propertyKey, value[, receiver])`

|    参数     |                 含义                 | 必选 |
| ----------- | ------------------------------------ | --- |
| target      | 获取原型的目标对象                    | Y    |
| propertyKey | 设置的属性的名称                      | Y    |
| value       | 设置的值                             | Y    |
| value       | 如果遇到 setter，this 将提供给目标调用 | N    |

```js
// Object
var obj = {};
Reflect.set(obj, "prop", "value"); // true
obj.prop; // "value"    
// Array
var arr = ["duck", "duck", "duck"];
Reflect.set(arr, 2, "goose"); // true
arr[2]; // "goose"  
// It can truncate an array.
Reflect.set(arr, "length", 1); // true
arr; // ["duck"];    
// With just one argument, propertyKey and value are "undefined".
var obj = {};
Reflect.set(obj); // true
Reflect.getOwnPropertyDescriptor(obj, "undefined");
        // { value: undefined, writable: true, enumerable: true, configurable: true }
```

### Reflect.set​PrototypeOf()

- `Reflect.setPrototypeOf` 方法改变指定对象的原型 （即，内部的 `[[Prototype]]` 属性值）
- 语法 `Reflect.setPrototypeOf(target, prototype)`

|   参数    |              含义              | 必选 |
| --------- | ------------------------------ | --- |
| target    | 目标对象                       | Y    |
| prototype | 对象的新原型 （一个对象或 null） | Y    |

```js
Reflect.setPrototypeOf({}, Object.prototype); // true
// It can change an object's [[Prototype]] to null.
Reflect.setPrototypeOf({}, null); // true  
// Returns false if target is not extensible.
Reflect.setPrototypeOf(Object.freeze({}), null); // false  
// Returns false if it cause a prototype chain cycle.
var target = {};
var proto = Object.create(target);
Reflect.setPrototypeOf(target, proto); // false
```

- 对于以上所有 `API` 第一个参数是 `Object` 的，如果给定的不是 `Object` 则抛出一个 `TypeError` 异常

**阅读**
<ol>
        <li><a href="https://ponyfoo.com/articles/es6-reflection-in-depth">ES6 Reflection in Depth</a></li>
        <li><a href="https://www.stefanjudis.com/today-i-learned/the-global-reflect-object-its-use-cases-and-things-to-watch-out-for/">Reflect Usage</a></li>
</ol>