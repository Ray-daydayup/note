## Vue响应式

[官方文档：深入响应式原理](https://cn.vuejs.org/v2/guide/reactivity.html)

- 监听数据变化，更新视图
- `Object.defineProperty`的使用
- 如何实现响应式
- `Object.defineProperty`的一些缺点和 Vue3.0 的 `Proxy`

### `Object.defineProperty`的使用

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

语法：`Object.defineProperty(obj, prop, descriptor)`

```js
// 在对象中添加一个属性与数据描述符的示例
Object.defineProperty(o, "a", {
  value : 37,
  writable : true,
  enumerable : true,
  configurable : true
});

// 对象o拥有了属性a，值为37

// 在对象中添加一个属性与存取描述符的示例
var bValue;
Object.defineProperty(o, "b", {
  get : function(){
    return bValue;
  },
  set : function(newValue){
    bValue = newValue;
  },
  enumerable : true,
  configurable : true
});

o.b = 38;
// 对象o拥有了属性b，值为38

// o.b的值现在总是与bValue相同，除非重新定义o.b

// 数据描述符和存取描述符不能混合使用
Object.defineProperty(o, "conflict", {
  value: 0x9f91102, 
  get: function() { 
    return 0xdeadbeef; 
  } 
});
```

