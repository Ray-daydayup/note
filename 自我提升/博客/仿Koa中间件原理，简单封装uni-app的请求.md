## 前言

`uni-app`作为一个用`vue`语法的跨端框架，虽然有许多坑，但是入手还是比较简单的，开箱即用。我接触前端就是因为`uni-app`，导师要求做个`app`，当时啥都不懂，就带点`c`的基础就开始用它，然后边学边补前端基础。`uni-app`本身是没有请求拦截器的，在插件市场里有第三方的请求拦截器，各有特点，于是自己就仿照了`Koa`中间件原理封装了一下`uni-app`的请求。

## 思路分析

首先先看下最终实现后的请求的使用形式

```js

/**
 * 定义拦截器，做几个约定，1. 当请求被拦截时，建议 return false，提示调用请求被拦截
 * 						 2. 每个拦截器必须有返回值（ return await next() ），否则再此之前的拦截器获取不到返回值
 * 						 3. 拦截器必须定义为 async 函数
 * 						 4. 调用 next 函数时，建议加上 await ，否则会造成返回值丢失
 */

const http = new ZRequest()

/**
 * 设置请求全局配置
 */
http.setConfig((config) => {
	config.baseUrl = serviceUrl
})
/**
 * 注册请求拦截器
 */
http.requestUse(tokenAuth(getToken, ['/login'], reLaunchLogin))  // token验证的中间件
http.requestUse(mutipleBaseUrl([/^\//, 'http://192.168.123.89:8081/'])) //设置不同的 BaseUrl的中间件
// 用于调试，将请求和响应打印到控制台
http.requestUse(async function(config,next){
	const url = config.url
	const res = await next()
	console.log(config.baseUrl,'>>',url,' >>> ',res);
	return res
})
/**
 * 注册响应拦截器
 */
http.resposeUse(handleResponse) //响应处理
// 获取响应的数据
http.resposeUse(async (response, next) => {
	return response[1].data.data
})

export default http

/**** 业务中使用 ****/

const getBYJCList = async function(data) {
	const res = await http.post('/by/byjc/list', data)
	return res
}
```

可以看到封装后的请求主要有`setConfig`、`requestUse`、`resposeUse`三个主要的函数，分别用来实现拦截器的注册和公共配置的设置，请求拦截器和响应拦截器的实现都是函数，不同的是请求拦截器传入的参数是`config`和`next`，而响应拦截器是`response`和`next`，`next`控制响应的取消。其主要实现在于`ZRequest`类和`Koa`的`compose`函数。思路比较简单，首先需要一个对象来储存`config`，两个数组分别充当请求拦截器和响应拦截器的队列，当调用请求时先处理`config`，将`uni-app`的请求函数压入请求拦截器队列，然后在调用`compose`函数将请求拦截器队列的函数组合，传入`config`执行；等到响应返回后，在利用`compose`函数将响应拦截器队列的函数组合传入`response`执行。

## 代码实现

**`compose`函数的实现**，这个是关于 `Koa`中间件原理的，具体怎么实现不细说了，详细的请看整理👉[传送门](https://github.com/zhangxiang958/zhangxiang958.github.io/issues/34)

```js
/**
 * @param {array} interceptor
 * @return {funtion}
 * next指向下一个函数，所以每次执行的时候需要将下一个函数传入当前函数，利用递归将其组合为一个函数
 */
function compose(interceptor) {
	return (ctx) => {
		const dispatch = (i) => {
			const fn = interceptor[i];
			try {
				return Promise.resolve(fn(ctx, dispatch.bind(null, i + 1)));
			} catch (error) {
				return Promise.reject(error);
			}
		};
		// 返回 promise
		return dispatch(0);
	};
}
```

### 请求基类的实现

```js
class ZRequest {
	constructor() {
		this.config = {
			baseUrl: "",
			header: {
				'content-type': 'application/json'
			},
			method: 'GET',
			dataType: 'json',
			sslVerify: false
		}
		this.requestInterceptor = [] //请求拦截器队列
		this.responseInterceptor = [] //响应拦截器队列
	}
	/**
	 * @param {Function} callback
	 * 设置全局配置
	 */
	setConfig(callback) {
	    // 利用引用类型的特性修改 config
		callback(this.config)
	}
	/**
	 * @param {Function} callback
	 * 请求拦截器注册，末尾一定要返回config,接收一个函数（config, next）=> { }。第一个参数为发送前的config,第二个参数为函数，调用则继续向下执行
	 */
	requestUse(...args) {
		this.requestInterceptor.push(...args)
	}
	/**
	 * @param {Function} callback
	 * 响应拦截器，传接收一个函数（response,next）=> {}
	 */
	resposeUse(...args) {
		this.responseInterceptor.push(...args)
	}
	/**
	 * 请求函数（核心代码）
	 */
	async request(options = {}) {
		options = this.getOptions(options) // 获取 config
		const request = this.getRequest() // 获取请求函数
		const requestQuene = [...this.requestInterceptor, request] // 请求拦截器和请求函数组合成请求任务队列
		const requestFn = compose(requestQuene) // 核心 compose
		const response = await requestFn(options) //传入config执行
		if (!response) {
		// 请求失败之间返回false
			return false
		}
		// 响应拦截器和响应函数组合成请求任务队列
		const responseQuene = [...this.responseInterceptor, async (response) => {
		    // 传入这个函数的目的是因为在 compose 函数的实现中，未考虑最后一个函数的下一个函数不存在，所以这个函数不传入next避免报错
			return response
		}]
		const responseFn = compose(responseQuene)
		const result = await responseFn(response)
		return result
	}
    /**
	 * 获取请求函数
	 */
	getRequest() {
	// 请求函数不传入next 是因为在 compose 函数的实现中，未考虑最后一个函数的下一个函数不存在，所以这个函数不传入next避免报错
		return async (options) => {
			options.url = this.getUrl(options.url, options.baseUrl) //获取请求的路径
			// 调用uni-app内部封装的promise
			const response = await uni.request({
				url: options.url,
				dataType: options.dataType,
				data: options.data,
				header: options.header,
				method: options.method,
				sslVerify: options.sslVerify
			})
			// response是一个数组 [err,res],详情请看官方文档
			return response
		}
	}
	/** 
	 * 检验请求是否是绝对路径
	 */
	checkUrl(url) {
		return /(http|https):\/\/([\w.]+\/?)\S*/.test(url)
	}
	/**
	 * 获取请求路径
	 */
	getUrl(url, baseUrl) {
	    // 绝对路径直接发送不拼接 baseurl
		const reqUrl = this.checkUrl(url) ? url : `${baseUrl}${url}`
		return reqUrl
	}
	/**
	 * @param {Object} options 为调用请求时传进来的option
	 * @return {Object}
	 * 获取请求的 config
	 */
	getOptions(options) {
		options.baseUrl = options.baseUrl || this.config.baseUrl
		options.dataType = options.dataType || this.config.dataType
		options.data = options.data || {}
		options.header = options.header || {}
		Object.assign(options.header, this.config.header) //为了保证不修改实例中的config，用浅拷贝,防止引用类型被改
		options.method = options.method || this.config.method
		options.sslVerify = options.sslVerify === undefined ? this.config.sslVerify : options.sslVerify //因为是boolean类型
		return options
	}
}
```

### 实现get、post

实现`restful`形式的api就比较简单了，只需要继承请求基类，添加对应的请求参数组合请求配置然后调用 `request`函数即可

```js
import ZRequest from './requestClass.js'

class RestClass extends ZRequest {
	constructor() {
		super()
	}
	async get(url, data, options = {}) {
		const method = "GET"
		return await this.request({
			url,
			data,
			method,
			...options
		})
	}
	async post(url, data, options = {}) {
		const method = "POST"
		return await this.request({
			url,
			data,
			method,
			...options
		})
	}
}

export default RestClass
```

## 番外，几个中间件实现的示例

1. `token`验证拦截器

```js
class TokenAuth {
	constructor() {
		// 排除规则，元素可以是正则
		this.exclusionRules = []
	}
	/**
	* 排除规则
	*/
	unless(arr = []) {
		this.exclusionRules = arr
		return this //链式调用
	}
	/**
	 * @param {String} url 请求路径
	 * @return {Boolean} flag 校验结果，true 为需要 token 校验，false 为不需要
	 * 需要验证则返回 true
	 */
	checkUrl(url) {
		// 空数组时全部校验返回 true
		// 找到对应的 url 则 返回 false
		const flag = this.exclusionRules.some(item => {
			if (typeof item === "string") {
			    // 传入的是路径字符串
				return item === url
			}
			if (item instanceof RegExp) {
			    //传入的是正则表达式
				return item.test(url)
			}
			return false
		})

		return !flag
	}
}

/**
 * @param {Function}  获取token的函数  
 * @param {Array}  要排除的 url数组
 * @param {Function}   校验未通过的回调函数
 */
const tokenAuth = (getToken, unless = [], callback) => {
	const tokenChecker = new TokenAuth()
	return async (config, next) => {
		const flag = tokenChecker.unless(unless).checkUrl(config.url)//是否需要校验
		if (flag) {
			const token = getToken() //获取token
			if (token) {
				config.header.Authorization = `Bearer ${token}` //设置token
				return await next() // 执行下一个拦截器
			}
			//未获取的提示
			uni.showToast({
				icon: 'none',
				title: '未获取到登陆凭证,请重新登录',
				duration: 2000
			});
			// 未获取的回调
			if (callback) {
				callback(config)
			}
			return false

		}
		return await next() // 执行下一个拦截器
	}
}

export default tokenAuth

```

2. 多个`baseUrl`请求拦截器

```js
// 参数为数组对的形式传入[/^\api1//, 'http://192.168.123.89:8081/'],[/^\api2//, 'http://192.168.123.89:8082/']
const mutipleBaseUrl = function (...args){
// args 为rest参数所以应该为二维[[/^\api1//, 'http://192.168.123.89:8081/'],[/^\api2//, 'http://192.168.123.89:8082/']]
	const urlMap = new Map(args) //设置键值对，因为Map键可以是正则
	const keys = Array.from(urlMap.keys()) //获取所有匹配的正则转换为数组
	return async (config, next) => {
		for (let i = 0; i < keys.length; i++) {
			if(keys[i].test(config.url)){
				let matched = config.url.match(keys[i])[0]
				config.baseUrl = urlMap.get(keys[i])//设置请求的baseUrl
				config.url = config.url.replace(matched,'')//设置请求的Url将识别项（\api1和\api2）去掉
				break//匹配到就停止循环不继续找了
			}
		}
		return await next() // 执行下一个拦截器
	}
}

export default mutipleBaseUrl
```

## 总结

仿照了`Koa`中间件原理封装`uni-app`的请求拦截器就完成了，只是简单的封装和思路展示，没有考虑不同平台的兼容性和更灵活的配置，如果有不足之处请指正🙏

