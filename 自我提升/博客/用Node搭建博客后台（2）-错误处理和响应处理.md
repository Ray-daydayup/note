## 前言

继续我的自建博客之旅， `node`搭建博客后台，采用 `koa` + `mysql`来实现。
一个后端api项目，对于响应的处理是必不可少的，与之相关联的就是异常的处理。如何捕获异常和返回期望的响应？🤔我懂得不多，只能按照我的目前的水平封装下。

项目源码：[https://github.com/Ray-daydayup/myblog-backend](https://github.com/Ray-daydayup/myblog-backend)

## 实现原理

`Koa`的 `async`和`await`用起来很香，关于`async`的异常处理我就有点搞不懂，尤其是在[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)上的描述，小白表示难以理解╮(╯▽╰)╭。关于异常处理，在掘金上有个大佬写的很棒👉[传送门](https://juejin.im/post/589036f8570c3500621a3be2#heading-10)👈。

他在文章里提到了几点：

1. 使用 `try catch` 捕获异常。
2. 默认不捕获错误的话，错误会一直冒泡到顶层，最后抛出异常，业务场景中，建立一种机制捕获最顶层的错误进行统一处理。

虽然我不懂里面那么多高深的知识，和大佬的写法，但是根据这个，我产生了我自己的处理思路。

`Koa`的中间件机制（[传送门](https://github.com/zhangxiang958/zhangxiang958.github.io/issues/34)👈这里写的很好）可以让我们很好的在顶层处理错误。我们只要在最外层放上一层`try catch`，配合`koa`提供的`ctx.throw([status], [msg], [properties])`来实现响应处理和异常处理。

## 代码实现

我只要定义一个中间件放在最顶层，我的处理中间件是这样的👇

```js
const { ErrorModel } = require('../models/http-error')
// 格式化错误响应
const format = (err, ctx) => {
  ctx.response.status = err.statusCode
  ctx.response.body = {
    code: err.code,
    msg: err.message || err.msg,
    request: ctx.method + ' >> ' + ctx.url
  }
}

module.exports = async (ctx, next) => {
  try {
    await next()
  } catch (err) {
  //判断是否为已知错误
    if (err.flag === 'ErrorModel') {
      format(err, ctx)
    } else {
    // 对于未知的错误返回统一的消息
      console.log(err)
      format(new ErrorModel(), ctx)
    }
  }
}
```

其实处理逻辑很简单，就是利用`ctx.throw`抛出错误，在顶层捕捉，然后判断错误类型，按格式返回响应。

那么重点就是如何定义`ErrorModel`了，看这里👇

```js
class ErrorModel {
	constructor(code = 500, msg = "未知服务器错误", statusCode = 500) {
		this.code = code //data携带的内部异常状态码
		this.msg = msg // 消息
		this.statusCode = statusCode //外层的状态码
	}
	throwErr(ctx) {
	//抛出错误
		ctx.throw(this.statusCode, this.msg, {
			code: this.code,
			flag: "ErrorModel",
		})
	}
}
// 400参数错误
class ParameterError extends ErrorModel {
	constructor(code, msg = "请求错误") {
		super(code, msg, 400)
	}
}
// 401错误
class AuthError extends ErrorModel {
	constructor(code, msg = "token认证失败") {
		super(code, msg, 401)
	}
}
// 404
class NotFoundError extends ErrorModel {
	constructor(code, msg = "未找到该api") {
		super(code, msg, 404)
	}
}
// 500
class InternalServerError extends ErrorModel {
	constructor(code, msg = "服务器内部错误") {
		super(code, msg, 500)
	}
}

module.exports = {
	ErrorModel,
	ParameterError,
	AuthError,
	NotFoundError,
	InternalServerError,
}
```

定义好了之后，我们只要在发生错误的地方创建对应的实例，然后调用`throwErr`函数就行了。

### 200的响应的处理

如果没有发生异常的话，那么就应该把数据包装好响应返回了，我也定义了一个类来实现统一格式的响应👇

```js
class SuccessModel {
  constructor(code, msg, data) {
    this.code = code || 200
    this.msg = msg || '操作成功'
    if (data) {
      this.data = data
    }
  }
  success(ctx) {
    // 所有的响应都是json，koa处理好的方式，直接用
    ctx.body = this
  }
}

module.exports = SuccessModel
```

成功响应后，创建实例，然后调用`success`函数就OK了。

### 常用状态码的进一步封装

在项目中，状态码的定义是约定好的，我们在返回响应信息时候，如果再去查找规范写状态码和消息就非常麻烦了，所以我们通常会封装好

```js
const SuccessModel = require("../models/http-success")
const {
	ParameterError,
	AuthError,
	NotFoundError,
	InternalServerError,
} = require("../models/http-error")
// 200 请求成功
const SUCCESS = async (ctx, data, msg) =>
	new SuccessModel(200, msg, data).success(ctx)
// 权限限制
const USER_NO_PERMISSION = async (ctx, msg = "没有权限") =>
	new SuccessModel(2100, msg).success(ctx)
// 用户错误
const USER_NOT_LOGIN = async (ctx) =>
	new SuccessModel(2001, "用户未登录").success(ctx)
const USER_ACCOUNT_EXPIRED = async (ctx) =>
	new SuccessModel(2002, "账号已过期").success(ctx)
const USER_ACCOUNT_DISABLE = async (ctx) =>
	new SuccessModel(2003, "账号不可用").success(ctx)
const USER_ACCOUNT_NOT_EXIST = async (ctx) =>
	new SuccessModel(2004, "账号不存在").success(ctx)
const USER_ACCOUNT_ALREADY_EXIST = async (ctx, msg = "账号已存在") =>
	new SuccessModel(2005, msg).success(ctx)
const USER_ACCOUNT_USE_BY_OTHERS = async (ctx) =>
	new SuccessModel(2006, "账号下线").success(ctx)
const USER_PWD_ERROR = async (ctx) =>
	new SuccessModel(2007, "密码错误").success(ctx)

// 400
const PARAM_NOT_VALID = async (ctx, msg = "请求参数无效") =>
	new ParameterError(1001, msg).throwErr(ctx)
const PARAM_IS_BLANK = async (ctx, msg = "请求参数为空") =>
	new ParameterError(1002, msg).throwErr(ctx)
const PARAM_TYPE_ERROR = async (ctx, msg = "请求参数类型错误") =>
	new ParameterError(1003, msg).throwErr(ctx)
const PARAM_NOT_COMPLETE = async (ctx, msg = "请求参数缺失") =>
	new ParameterError(1004, msg).throwErr(ctx)
// 401
const TOKEN_IS_BLANK = async (ctx) =>
	new AuthError(4004, "token为空").throwErr(ctx)
const TOKEN_EXPIRED = async (ctx) =>
	new AuthError(4001, "token过期").throwErr(ctx)
const TOKEN_INVALID = async (ctx) =>
	new AuthError(4002, "token无效").throwErr(ctx)
const AUTHENTICATION_FAIL = async (ctx, msg = "认证失败") =>
	new AuthError(4003, msg).throwErr(ctx)
// 404
const NotFound = async (ctx) =>
	new NotFoundError(
		404,
		"未找到api，请检查请求路径以及请求方法是否出错"
	).throwErr(ctx)

// 500
const FAIL = async (ctx, msg) => new InternalServerError(500, msg).throwErr(ctx)
const FILE_UPLOAD_FAIL = async (ctx) =>
	new InternalServerError(5001, "文件上传失败").throwErr(ctx)
```

在上面代码中把常用的状态码进行了封装，每种状态码都语义化了，需要时调用对应函数，传入`ctx`和一些信息。

## 业务使用

直接上代码（偷懒😁）
 
1. 抛出异常

```js
// 404
app.use(async (ctx, next) => {
    // 抛出404
	await NotFound(ctx)
})
```

2. 成功响应

```js
async function updateService(ctx, params) {
  const userInfo = ctx.state.user
  if (userInfo.permission !== 1) {
    // 调用
    await USER_NO_PERMISSION(ctx)
    return
  }
  if (params.hasOwnProperty('title')) {
    const selectRes = await articles.select(params.title)
    if (selectRes[0]) {
    // 调用
      await USER_ACCOUNT_ALREADY_EXIST(ctx, '文章标题已存在')
      return
    }
  }
  const articlesResult = await articles.update(params)
  // 调用
  await SUCCESS(ctx, {
    line: articlesResult.affectedRows || articlesResult[0].affectedRows
  })
}
```

## 总结

利用 `koa` 中间件机制实现全局的响应处理和异常处理，但还是存在，许多不足

- 自己的代码水平不够，有些代码写的不够好，慢慢进步，只是很早之前写的
- 对`koa`的了解不够，应该有更好的处理方法，还有一些特性没用到
- 如有错误和更好的意见，请留言，感谢🙏
