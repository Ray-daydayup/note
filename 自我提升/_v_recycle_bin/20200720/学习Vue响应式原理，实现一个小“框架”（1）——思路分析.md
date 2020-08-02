## 前言

这个同样是写博客的产出，之前在写博客的时候，瞎弄了一个模板引擎，当时就想之后把他优化一下，把 `Vue` 响应式原理放进去，弄成一个小的可以用来减少我写博客前端代码的组件化的”框架“，于是我就来填坑了。这个只是自己用来学习 `Vue2.0`响应式原理的一个小项目，代码的实现可能很初级，有些地方写的会比较差，请见谅。这篇文章先看下，最终实现的框架的成果。

项目源码：[传送门](https://github.com/Ray-daydayup/myblog-webpack/tree/master/src/lib/min-vue)
实现效果：[我的博客](http://raydaydayup.cn/)就是用这个小框架改造的

## 成果和功能展示

实现的这个小框架，是一个双向绑定的组件化的小框架，有跨组件通信，组件属性，事件绑定，数据绑定，列表渲染等功能，但是也存在很多缺陷的，比如没实现对 `html`中的模板绑定、组件属性没有声明等。只是一个学习的项目，勿怪哈，希望能给你们带来帮助。

### 定义和注册一个组件

仿照了`vue`的写法，实现了一个组件的定义和注册，并没有全局和局部组件的区分，都是全局组件。

```js
import MVue from '@/components/index'

const tagsLink = {
  name: 'tags-link', // 组件名，必需
  template: 'a[href="javascript:;]@click.stop=navTag:tag@{$tag.name$}', // 模板，必需
  data () { // 组件内部的响应式数据
    return {
       tag:{
           name: '标签'
       }
    }
  },
  created() { // 实例 mvue 创建的回调
    this.navTag(this.tag)
    this.$event.emmit('load-more') //跨组件通信的事件总线，触发事件
  },
  methods: { // 组件内部方法
    navTag(tag) {
      const target = './tag.html'
      this.$router.navTo(target, { //简单的路由
        id: tag.tagId
      })
    }
  }
}

MVue.Component(tagsLink) //注册组件
```

### 根实例的定义

定义了组件之后要在`html`中使用的话，需要定义一个根实例，一个完整的使用示例，如下代码书写

```html
<div id="app">
    <article-list :articles="articles"></article-list> // 组件书写，和属性传递，只有这个写法，没有在HTML中做响应式的绑定语法
    <load-more :pages="pages"></load-more>
</div>
```

```js
import MVue from '@/components/index'
import router from '@/utils/router.js'
import articleList from './article-list.js'
import loadMore from './load-more.js'

MVue.router = router // 把路由方法挂上去
// 注册使用组件
MVue.Component(articleList)
MVue.Component(loadMore)
const mVue = new MVue({
  el: '#app', // 
  data: {
    pages: {
      text: '获取更多',
      currentPage: 0,
      pageSize: 5,
      more: true
    },
    articles: []
  },
  created() {
    this.getArticles()
    this.$event.on('load-more', this.getArticles, this) //注册自定义事件
  },
  methods: {
    async getArticles() {
      if (!this.pages.more) {
        return
      }
      this.pages.currentPage++
      const res = await getArticleList(
        this.pages.currentPage,
        this.pages.pageSize,
        true,
        { articleState: 1 }
      )
      if (res.flag) {
        if (res.data.length < this.pages.pageSize) {
          this.pages.more = false
          this.pages.text = '没有更多了' 
        }
        this.articles.push(...res.data)
      }
    },
  }
})

//组件渲染，创建dom 把组件添加进DOM树
mVue.render(() => {
  document.querySelector('#mask').style.display = 'none'
})
```

### 组件模板语法

这个和我之前写的小模板引擎是类似的

1. **单个元素的属性和文本的定义**： `div[class="box]{hahaha}` 表示 `<div class="box">hahaha</div>`
   - `[]` 属性  =》 写法：`[class="box,id="box]`
   - `{}` 文本  =》 写法： `{hahaha}`
2. **数据绑定与过滤器**：`h6[class="font-$regular$]{ 创建时间：$article.createtime$ -- 修改时间：$article.modifiedtime:time$}`
    - `$data$` 两个`$`之间写上组件实例定义的变量名或者父组件传入的变量
    - `:`之后的为组件实例定义的方法，会将函数返回结果填充载`$`之间
3. **事件的绑定**：`a@click.stop=nav:article@[href="javascript:;,class="title]`
    - 两个`@`之间为组件绑定的事件部分`事件名.stop=方法名:参数1:参数2`
    - 只有阻止冒泡的功能，传入的参数可以有多个也可以不传，可以是变量名，也可以是字符串
4. **`innerHtml`的使用**：`div[class="markdown-body,style="padding: 0;]^article.abstract:markdown^`
    - 两个`^`之间是对应的`html`绑定部分（会忽略`{}`）=》`变量名:方法名`
    - 内部的效果就是`div.innerHtml = 返回值`
5. **模板中自定义组件的使用**：`article-item[:article="item]`
    - 组件只要在渲染之前注册就可以在模板中使用
    - `[:article="item]`表示 `:组件的属性名="当前组件的变量名`，属性名绑定后可以在组件中直接使用该变量名（没有声明），属性可以层层传
6. **元素层级关系**：`div>(a+article-item)`
    - `>` 父子关系 
    -  `+` 兄弟关系  
    - `()` 优先运算同一层级
7. **列表渲染的使用**
    - 由于列表渲染比较难搞我就简单实现了一下，有许多限制
    - 只能针对自定义组件列表渲染
    - 不能在`html`中使用
    - 列表渲染组件的前面必须有父级元素
    - 使用示例

```js
// html中使用
<div id="app">
    <tags-list></tags-list> // 组件书写
</div>
/******** 组件定义 ********/
//父组件
{
  name: 'tags-list',
  template: 'div>tags-link%item in tags%[:tag="item]',
  data () { // 组件内部的响应式数据
    return {
       tags:[{name: 1},{name: 2}]
    }
  },
}
//要渲染的子组件定义
{
  name: 'tags-link',
  template: 'a[href="javascript:;]@click.stop=navTag:tag@{$tag.name$}',
}
```

那么最终渲染的结果就是

```html
<div>
    <a href="javascript:;">1</a>
    <a href="javascript:;">2</a>
</div>
```

## 整体思路

实现这个小的组件化框架，主要分为三个部分：响应式的实现、模板编译的实现和`MVue`类的实现。

- 响应式的实现：主要是实现`vue2.0`的响应式原理，对响应式数据实现监听。
- 模板编译：主要是实现如何根据模板语法生成DOM元素和添加响应式依赖。
- `MVue`类： 主要是负责根据传入的用户数据来整合响应式和自定义组件的管理

组件化的思路：利用`MVue`类的静态属性，实现一个全局唯一对象，存储组件对应的组件工厂实例（自己起的😂）和响应式实例的配置的对组件实现管理。当注册组件的时候根据对应的配置生成对应的响应式实例配置和组件工厂实例，并保存起来。当组件根实例被挂载时，查找对应的组件，利用组件工厂实例和对应的响应式配置，生成 `DOM`片段，然后挂载到`DOM树`对应的位置。

整体的架构如下图所示

![MVue（类）](_v_images/20200720192653311_28346.jpg)

## 总结

这个小框架总体实现的思路和实现的功能大概就是这些，一些细节后续的文章慢慢写，后面会写响应式原理的实现和一些组件编译的东西，不是所有东西都是从`vue`源码来的，有些东西代码写的很差劲，见谅。持续学习总结，希望自己一直在前进路上……

项目源码：[传送门](https://github.com/Ray-daydayup/myblog-webpack/tree/master/src/lib/min-vue)
实现效果：[我的博客](http://raydaydayup.cn/)就是用这个小框架改造的