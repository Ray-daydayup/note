---
typora-copy-images-to: media
---

> 第02阶段.前端基本功.前端基础.js进阶

# 基础语法

## 学习目标


* 了解


     重新认识instanceof
    
    - 原型链不可变
    - for..in的问题

* 重点
* 重点

  * 上下文调用模式

  * js中的继承

  * 闭包

    ​

## 1. 重新认识instanceof 

js基础阶段对instanceof作用的描述 : **判断一个对象是否是某个构造函数的实例**

现在我们学习了原型,也学习了原型链,所以我们现在可以更严谨的描述他的作用:

**判断一个函数的原型对象,是否在实例对象的原型链上**

```javascript
var arr = [];
console.log(arr instanceof Array); //true
console.log(arr instanceof Object);  //true
```



## 2. 原型链不可变 
**一个实例,一旦创建出来,他的原型链就固定了,不会随着函数的原型的改变而改变.这就是原型链不可变**
```javascript
function Person(){
}
var p1 = new Person();
console.log(p1 instanceof Person); // ?

Person.prototype = {
    constructor : Person,
    a : 1
}
console.log(p1 instanceof Person); // ?
var p2 = new Person();
console.log(p2 instanceof Person); // ?
```

<img src="media\原型链不可变.png">

## 3. 上下文调用函数

### 3.1 回顾之前我们学习的调用函数的几种方式

- 普通调用

> this -- > window

```javascript
  function fn(){
      console.log(this);  // window
  }
  fn();
```

- 对象调用

> this -- > obj

```javascript
  function fn(){
  	console.log(this) // obj
  }
  var obj = {
      f : fn
  };
  obj.f();

```

- 构造函数调用

> this -- > 构造函数的实例

```javascript
  function Person(name){
      this.name = name; //this --> 实例
  }

  var p1 = new Person('zs');
```

- 定时器中的回调函数

> this -- > window

```javascript
  setTimeout(function(){
  	console.log(this)// window
  },1000);
```

- 事件处理函数调用

> this --> 事件源

```javascript
  box.onclick = function(){
  	console.log(this); // box
  }
```

  ​

**我们总结了一句话: this的指向是在函数调用时决定的. 谁调用这个函数,this指向谁**

### 3.2 上下文调用函数的三个方法 

>为什么要学习上下文调用函数?
>
>因为这样我们就可以随意的控制函数中this的指向



#### 3.2.1  call方法 

**作用:** 调用该函数,并修改函数中this的指向

**语法:** 函数名. call(对象,[实参]);

**参数详解:**

第一个参数: 要让函数中this指向谁,就写谁

后面的参数: 被调用函数要传入的实参,以逗号分隔

```javascript
function fn(x, y){
    console.log(this); // ['a','b','c']
    console.log(x + y); //8
}
fn.call(['a','b','c'], 3, 5);
```

#### 3.2.2 apply 方法

**作用:** 调用该函数,并修改函数中this的指向

**语法:** 函数名. apply(对象,数组);

**参数详解:**

第一个参数: 要让函数中this指向谁,就写谁

第二个参数: 要去传入一个数组,里面存放被调用函数需要的实参

```javascript
function fn(x, y){
    console.log(this); // {a : 1}
    console.log(x + y); //8
}
fn.apply({a : 1}, [3, 5]);
```

#### 3.2.3 bind方法

**作用:** 不调用函数,克隆一个新的函数,并修改新函数中this的指向,将新的函数返回

**语法:** 函数名. bind(对象[,实参]);

**参数详解:**

第一个参数: 要让函数中this指向谁,就写谁

后面的参数: 被调用函数要传入的实参,以逗号分隔

```javascript
function fn(x, y){
    //调用newFn时打印结果
    console.log(this);  //{b:2}
    console.log(x + y); //8
}

var newFn = fn.bind({b:2}, 3, 5);
newFn();
```

#### 小结:

- call和apply 会调用函数 ,  bind不会调用函数
- apply要求把实参放到数组中,然后把数组传进来
> 当我们需要自己调用函数,并且要修改函数中this的指向的时候, 用call / apply
> 当我们不需要自己调用函数,要浏览器帮我们调用(事件处理函数, 定时器的回调函数),
> 并且要修改函数中this的指向,我们就可以用bind

#### 让伪数组可以使用数组的方法

```js
var result = Array.prototype.filter.call(divs,function(item,index,arr){
        return item.className === 'one';
    });
console.log(result);
```
#### bind使用场景

```js
 // 需求: 给每一个li注册一个点击事件,点击这个li之后,每隔一秒钟,打印一下当前li中的文本
        // 1.1 获取元素
        var lis = document.getElementsByTagName('li');
        // 1.2 给每一个li注册点击事件
        for(var i = 0 ; i < lis.length; i++){
            lis[i].onclick = fn;
        }        
        //每一个li的事件处理函数
        function fn(){
            // console.log(this);// --> 点击的那个li
            // 1.3 在事件处理函数中,设置一个定时器
            setInterval(function(){
                // 1.4 在定时器的回调函数中,打印当前li的文本
                console.log(this.innerText);
                // console.log(this); //-->window
            }.bind(this), 1000);
```

#### bind的注意点
**当new 和 bind返回的函数一起使用的时候,函数中this指向的还是实例**
```js
function fn(){
            console.log(this);
        }

        var f = fn.bind([1,2,3]);
        f(); //this --> [1,2,3]
        var obj = {};
        obj.f = f;
        obj.f(); //this --> [1,2,3]

         //当new 和 bind返回的函数一起使用的时候,函数中this指向的还是实例
        var newf = new f(); //this --> 实例
```

## 4. for..in的问题

> for...in 遍历时,会把原型上的属性也遍历出来

```javascript
function Person(name){
    this.name = name;
}

Person.prototype.type = 'human';

var zs = new Person('zs');

for(var key in zs){
    console.log(key); // name , type  把type也打印出来了
}
```

如果我们就想打印对象自己身上的属性,可以使用hasOwnProperty方法来解决

**hasOwnProperty的作用:** 判断是不是对象自己的属性

**hasOwnProperty的语法：** 对象.hasOwnProperty('属性') 是则返回true, 否则false

```javascript
function Person(name){
    this.name = name;
}

Person.prototype.type = 'human';

var zs = new Person('zs');

for(var key in zs){
    if(zs.hasOwnProperty(key)){
        console.log(key);   //只有自己的name
    }
}
```

## 5. js中的继承

### 5.1 为什么要学习js中的继承

此时我们希望son对象,也拥有far对象的money, house这些属性,但是我们不想再重新写一遍,怎么办?

```javascript
var far = {
    money : $1000000000,
    house : ['别墅', '大别墅', '有游泳池的打别墅', '城堡'];
}

var son = {
    
}
```



### 5.2 两个对象之间的继承 

#### 5.2.1 遍历对象 

```javascript
var far = {
    wife : '小龙女',
    money : 1000000000,
    house : ['别墅', '大别墅', '有游泳池的打别墅', '城堡']
}

var son = {
    wife : '小黄飞',
}

//通过for..in遍历far
for(var key in far){
    son[key] = far[key];
}

console.log(son); // 此时son拥有了money和house .

```

**但是以上代码书写存在一个问题 : 把wife的值也继承下来了.我们希望自己有的,就不继承了.所以我们要改一下代码**

```javascript
for(var key in far){
    //自己没有的才继承
    if(!son.hasOwnProperty(key)){
        son[key] = far[key];
	}
}
```

**这样处理解决刚才的问题,但是我们发现两个对象上面存在一样的代码.这样比较占用内存**

#### 5.2.2  通过create方法实现继承 

>通过ceate方法实现继承,可以避免遍历对象方式,占用内存的问题

**create方法语法:** Object.create(参考对象) 返回一个新的对象

**create方法的作用:** 返回的新的对象的`__proto__` 指向参考对象  

```javascript
var far = {
    wife : '小龙女',
    money : 1000000000,
    house : ['别墅', '大别墅', '有游泳池的打别墅', '城堡']
}

var son = Object.create(far);
son.wife = '小黄飞';
console.log(son.money); //1000000000
console.log(son.house); //['别墅', '大别墅', '有游泳池的打别墅', '城堡']
console.log(son.wife); // 小黄飞
console.log(son.__proto__ === far); //true
```

<img src="media\create方法图解.png">

 



### 5.3 构造函数的继承

>我们工作中要经常创建多个具有相同属性的对象,所以经常要写构造函数.
>
>那么构造函数创建的出来的对象该如何实现继承呢?

#### 5.3.1 借用构造函数

```javascript
function Person(name, age){
    this.name = name;
    this.age = age;
    this.sayHello = function(){
        console.log('hello, ' + '我是' + this.name );
    }
}

function Student(name, age, score){
    this.score = score;
    Person.call(this, name, age);//借用构造函数
}

var stu = new Student('zs', 18, 100);
console.log(stu); //{score : 100, name : zs, age : 18}
stu.sayHello(); //hello, 我是zs
```

**但是我们一般不会把方法写在构造函数的函数体内,我们把方法写在函数的原型上,那么这个时候构造借用函数就无法继承方法了,我们想要继承原型上的方法.可以用原型继承**

#### 5.3.2 原型继承

>用来继承方法

```javascript
function Person(name, age){
    this.name = name;
    this.age = age;
    this.sayHello = function(){
        console.log('hello, ' + '我是' + this.name );
    }
}

function Student(name, age, score){
    this.score = score;
}

Student.prototype = new Person();

var stu = new Student('zs', 18, 100);
console.log(stu); //{score : 100}
stu.sayHello(); //hello, 我是undefined
```

<img src="media\原型继承.png">  

**我们发现,方法继承下来了,但是属性却没有继承下来**

#### 5.3.3 组合继承

>借用构造函数 + 原型继承

```javascript
function Person(name, age){
    this.name = name;
    this.age = age;
    this.sayHello = function(){
        console.log('hello, ' + '我是' + this.name );
    }
}

function Student(name, age, score){
    this.score = score;
    Person.call(this, name, age);
}

Student.prototype = new Person();

var stu = new Student('zs', 18, 100);
console.log(stu); //{score : 100, name : zs, age : 18}
stu.sayHello(); //hello, 我是zs
```



## 6. 闭包

**概念:** 函数和函数作用域的结合

**通俗理解:** 内部函数使用外部函数的变量, 整个外部函数形成了一个闭包

### 6.1闭包的作用:

1. 私有化数据
2. 数据保持

```javascript
function main(){
    var money = 10000;  //放到局部作用中,防止全局变量污染(私有化数据)

    return {
        queryMoney : function(){
            return money;
        },
        payMoney : function(num){
            money -= num;
        },
        addMoney : function(num){
            money += num;
        }
    }
}

var moneyManger = main();  // 通过moneyManger 可以获取到局部的变量money
```



### 6.2闭包的缺点: 
  由于内部的函数使用了外部函数的变量,导致外部这个函数无法被回收掉.如果代码中大量的存在闭包,可能会导致内存泄露 (不要刻意使用闭包);


## 7.拓展内容

### 7.1 函数的静态成员和实例成员

成员: 泛指属性和方法

静态成员: 函数也是对象, 函数自己的属性

实例成员: 特指构造函数时,写在构造函数体内,通过this.xxx.给实例添加的属性

```javascript
function Person(){
	this.name = name; //实例成员
    this.age = age; // 实例成员
}

Person.type = '呵呵'; //静态成员
```



### 7.2.Function的原型图

Functon 自己创造了自己

Object是Function的实例

<img src="media\Function原型图01.png">





