## 前言

继续我的自建博客之旅， `node`搭建博客后台，采用 `koa` + `mysql`来实现。
这次弄一弄请求参数的验证👉

项目源码：[https://github.com/Ray-daydayup/myblog-backend](https://github.com/Ray-daydayup/myblog-backend)

## 起因

我之前一直用的`yapi`来模拟接口，它里面提供了接口验证的模拟，每次请求会验证参数提示我请求参数类型出错了。到了我自己写 `api` 的时候就有点抓瞎了，后台是怎么实现的？（这时我用`java`的同学笑了笑，你看，我就写个类型就好了😜。……保持微笑😊）

然后开始搜`koa`相关的插件，这些还是蛮多的，比如：`koa-parameter`、`koa2-validator`、`koa-middle-validator`等等，无助的我又陷入了选择困难。然后我本着我是学习的心理，写一个简单的参数验证吧（其实水平有限，不知道怎么去和我已有的东西结合起来，想用我自己封装的异常处理🙃）。

说到参数验证有个家伙是好像很出名（我搜的）那就是[`joi`](https://github.com/hapijs/joi)，他的使用方式大概是这样的（[代码来源](https://www.cnblogs.com/zzbo/p/5906101.html)）👇

```js
var Joi = require('joi');
var schema = Joi.object({
    username: Joi.string().min(3).max(30).required(),
    isA: Joi.boolean(),
    AVal: Joi.number(),
    isB: Joi.boolean(),
    BVal: Joi.string()
})
.with('isA', 'AVal')
.with('isB', 'BVal')
.without('isA', 'isB')
.or('isA', 'isB');

var input = {
	username: 'zzbo'
}

var output = Joi.validate(input, schema);
//error: ValidationError: "value" must contain at least one of [isA, isB]
```

当时我看到这样的写法的时候还是很惊讶的，作为一个小白，对于面向对象、链式调用这些东西都不清楚，这种东西真的冲击到了我，那我能不能尝试实现一下类似的用法呢？这个想法一产生就扑灭不了了，话不多说，就是干。

## 原理和代码实现

### 用法示例

首先，先上使用成品，水平有限，只实现了样子货👇

```js
// 定义校验规则
let schema = {
    page: new ParamCheck().isNumber().min(1).isRequired(),
    pagesize: new ParamCheck().isNumber().min(1).isRequired(),
    moreDetail: new ParamCheck().isBoolean().isRequired(),
    tagId: new ParamCheck().isNumber().min(1),
    categoryId: new ParamCheck().isNumber().min(1)
  }
// 等待校验结果，如果失败则会直接抛出异常（和我之前的异常处理结合了一下）
await ParamCheck.check(params, schema, ctx)
```

我只考虑了对于传递的`object`进行验证，虽然`new`看起来很丑，当时还是实现了我的需求。

### 思路分析

整体思路就是定义一个参数校验类，在初始化实例后调用对应的方法进行校验规则（我定义为实例的属性`rules`）的收集。通过定义一个规则对象，在调用类静态方法的时候循环取出每个规则定义，根据`key`去取值，然后根据规则集依次校验。主要的工作有两点：

- 规则的收集
- 校验规则的验证

#### 规则的收集

首先按照我的需求定义三类规则：**类型规则、长度规则和是否必须**。然后就是代码实现👇

```js
class paramCheck {
	constructor() {
		this.rules = {} //规则集 type:表示类型；min和max表示长度上下限；isRequired表示是否必须
	}
	// 校验静态方法
	static async check(params, schema, ctx) {
		const keys = Object.keys(schema)
		try {
		    // 循环取出规则集，进行校验
			for (let i = 0; i < keys.length; i++) {
				const key = keys[i]
				let rules = schema[key].rules //取出规则集
				rulesCheck(params, key, rules)//调用函数校验
			}
			return
		} catch (error) {
		    // 捕获抛出的错误
			switch (error.name) {
				case "paramNotComplete":
					await PARAM_NOT_COMPLETE(ctx, error.message) //之前定义的异常处理函数
					break
				case "paramTypeError":
					await PARAM_TYPE_ERROR(ctx, error.message)
					break
				case "paramNotValid":
					await PARAM_NOT_VALID(ctx, error.message)
					break
				default:
					await FAIL(ctx, error.message)
					break
			}
		}
	}
	// 类型规则
	isString() {
		this.rules.type = "string" //写入类型规则
		return this //返回this链式调用
	}
	isNumber() {
		this.rules.type = "number"
		return this
	}
	isBoolean() {
		this.rules.type = "boolean"
		return this
	}
	isObject() {
		this.rules.type = Object // object 和 array 特殊对待
		return this
	}
	isArray() {
		this.rules.type = Array
		return this
	}
	// 长度规则
	min(min) {
		this.rules.min = min //写入长度规则
		return this
	}
	max(max) {
		this.rules.max = max
		return this
	}
	// 是否必须
	isRequired() {
		this.rules.isRequired = true 
		return this
	}
}
```

#### 规则的验证

规则验证还是有一定顺序的，是否必须`>`类型规则`>`长度规则，所以得按照顺序校验。代码长这样

```js
function rulesCheck(params, key, rules) {
	if (!requiredCheck()) {
		return false
	}
	typeCheck()
	lengthCheck()
	return true
	function requiredCheck() {……}
	function typeCheck() {……}
	function lengthCheck() {……}
}
// 参数错误类
class ParamError extends Error {
	constructor(msg, name) {
		super(msg)
		this.name = name
	}
}

```

1. **是否必须**

```js
function requiredCheck() {
    //如果参数必须，但是没有，报错
    if (rules.isRequired && !params.hasOwnProperty(key)) {
        throw new ParamError(`请求参数${key}缺失`, "paramNotComplete")
    }
    if (!rules.isRequired && !params.hasOwnProperty(key)) {
        // 参数不必须，但是也没有，不继续向下校验
        return false
    }
    // 其他的情况都需要继续校验
    return true
}
```

2. **类型校验**

```js
function typeCheck() {
    if (rules.type) {
	    const type = rules.type
	    // 校验类型
		if (
			typeof params[key] === type ||
            (typeof type !== "string" && params[key] instanceof type)
        ) {
		    return
        }
        // 抛出错误
        throw new ParamError(`请求参数${key}类型错误`, "paramTypeError")
    }
}
```

3. **类型校验**

```js
function lengthCheck() {
	const min = rules.min ? rules.min : 0
	const max = rules.max ? rules.max : Infinity
	const type = rules.type
	let length = 0
	// 数组和字符串检验长度
	if (type === "string" || type === Array) {
		length = params[key].length
	}
	// 数字检验大小
	if (type === "number") {
		length = params[key]
	}
	if (length < min || length > max) {
		throw new ParamError(`请求参数${key}长度无效`, "paramNotValid")
	}
}
```

## 总结

对于参数的简单校验已经完成了，虽然很简陋，但是满足了我的需求。新手一枚，代码写得不足之处，请见谅🙏