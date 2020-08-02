## 实现

1.0

```js
// 触发更新视图
function updateView() {
  console.log('updateView')
}

/**
 * 监听数组
 * @param {Array | Object} data
 */
function observe(data) {
  if (typeof data !== 'object' || data === null) {
    // 不是对象或数组
    return data
  }

  for (const key in data) {
    defineReactive(data, key, data[key])
  }
}
/**
 * 定义响应式
 * @param {Array | Object} data 数据
 * @param {string} key 属性名
 * @param {*} val 值
 */
function defineReactive(data, key, val) {
  observe(val) // 递归深度遍历，实现深度监听
  Object.defineProperty(data, key, {
    enumerable: true, // 可枚举
    configurable: false, // 当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，同时该属性也能从对应的对象上被删除。默认为 false。
    get: function () {
      // val 闭包
      return val
    },
    set: function (newVal) {
      observe(newVal) // 递归深度遍历，实现深度监听

      // val 一直在闭包中，此处设置完之后，再 get 时也是会获取最新的值
      val = newVal
      updateView()
    }
  })
}

/****
 * 测试用例
 */

const data = {
  a: 1,
  b: '1',
  c: {
    d: 1
  }
}

observe(data)

data.a = 3 // log: updateView
data.b = 3 // log: updateView
delete data.a
console.log(data) // 属性删不掉, configurable 为 true 时，能被删除，但不会触发更新
data.c.d = { dd: 2 } // log: updateView
data.c.d.dd = 3 // log: updateView

```

**`Object.defineProperty`缺点**

- 深度监听使用递归，一次性计算量大（可解决）
- 新增或者删除属性时监听不到（Vue.set Vue.delete）
- 无法监听数组，特殊处理

## 实现对数组的监听

对数组的原型进行拦截，对常用方法进行响应式处理

```js
// 重新定义数组原型
const oldArrayProperty = Array.prototype
// 创建新对象，原型指向 oldArrayProperty ，再扩展新的方法不会影响（污染）原型
const arrProto = Object.create(oldArrayProperty)
;['push', 'pop', 'shift', 'unshift', 'splice'].forEach((methodName) => {
  arrProto[methodName] = function (...args) {
    updateView()
    oldArrayProperty[methodName].call(this, ...args)
  }
})
/**
 * 监听
 * @param {Object} data
 */
function observe(data) {
  if (typeof data !== 'object' || data === null) {
    // 不是对象或数组
    return data
  }
  if (Array.isArray(data)) {
    data.__proto__ = arrProto
  }

  for (const key in data) {
    defineReactive(data, key, data[key])
  }
}
/**
 * 定义响应式
 * @param {Array | Object} data 数据
 * @param {string} key 属性名
 * @param {*} val 值
 */
function defineReactive(data, key, val) {
  observe(val) // 递归深度遍历，实现深度监听
  Object.defineProperty(data, key, {
    enumerable: true, // 可枚举
    configurable: false, // 当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，同时该属性也能从对应的对象上被删除。默认为 false。
    get: function () {
      // val 闭包
      return val
    },
    set: function (newVal) {
      observe(newVal) // 递归深度遍历，实现深度监听

      // val 一直在闭包中，此处设置完之后，再 get 时也是会获取最新的值
      val = newVal
      updateView()
    }
  })
}

// 触发更新视图
function updateView() {
  console.log('updateView')
}

/****
 * 测试用例
 */

const data = {
  a: 1,
  b: '1',
  c: {
    d: []
  }
}

observe(data)

data.a = 3 // log: updateView
data.b = 3 // log: updateView
delete data.a
console.log(data) // 属性删不掉, configurable 为 true 时，能被删除，但不会触发更新
data.c.d.push(3, 4, 5) // log: updateView
data.c.d[0] = 2 // 无法监听

```