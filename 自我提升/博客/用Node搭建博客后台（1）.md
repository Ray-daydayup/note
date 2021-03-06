## 前言

继续我的自建博客之旅，前端手写出来了，用`php`搭的一个简单的后台（用 `.txt`当数据库😂 ），于是简单学习了 `node`准备搭建博客后台。
采用 `koa` + `mysql`来实现。首先，来考虑写后台有哪些功能……👉（ps：本篇文章几乎没代码）

项目源码：[https://github.com/Ray-daydayup/myblog-backend](https://github.com/Ray-daydayup/myblog-backend)

## 项目需求分析

虽然说的很高大上，分析啥项目需求，其实就是自己后台需要哪些功能👇

- 文章的增删改查
- 标签的增删改查
- 分类的增删改查
- 用户的增删改查（虽然只有一个用户，但是还是做了，后台管理系统需要登录😊）

那么其实整个博客的所有功能都围绕文章、标签、分类和用户四个对象来的，那么这几个对象的关系是什么呢？🤔

1. 文章和标签（多对多关系）
    - 一篇文章可能对应多个标签
    - 一个标签可能对应多篇文章
2. 文章和分类（多对一）
    - 一篇文章只有一个分类
    - 一个分类有多篇文章、
3. 用户和其他对象的关系
    - 原本应该有关系的，但是个人博客作者只有自己，那么就没有太多关联了（我又偷懒了✌）

## 设计数据库

既然功能理清了，那么就是设计数据库了（原谅我又偷懒了，没写类型啥的，因为手敲 `markdown` 表格太难了，手疼😭）

1. 文章表

|     字段     |   注释   |
| ------------ | ------- |
| id           | 主键id   |
| title        | 文章标题 |
| abstract     | 摘要     |
| content      | 内容     |
| createtime   | 创建时间 |
| modifiedtime | 修改时间 |
| categoryid   | 分类id   |
| state        | 文章状态 |

2. 标签表

|     字段     |   注释   |
| ----------- | ------- |
| id          | 主键id   |
| name        | 名称     |
| description | 描述    |
| state       | 标签状态 |

3. 分类表（别意外，它就和标签表一样😁，因为这样他们好多功能就重复了，就可以省写好多代码）

|     字段     |   注释   |
| ----------- | ------- |
| id          | 主键id   |
| name        | 名称     |
| description | 描述    |
| state       | 标签状态 |

4. 用户表（有权限是因为想着后面要给别人看吧，于是弄了一个，标记除了我以外的人，他们不能修改我的东西，我没有做前端权限，做在了后端）

|    字段    |   注释   |
| ---------- | ------- |
| id         | 主键id   |
| username   | 用户名   |
| password   | 密码     |
| state      | 帐号状态 |
| permission | 权限     |

5. 文章和标签对应表（用来解释他们多对多的关系）

|    字段    |  注释  |
| ---------- | ------ |
| id         | 主键id |
| article_id | 文章id |
| tag_id     | 标签id |

**注意事项**

- 因为我现在文章里用输入法表情（微软输入法自带的，按`ctrl+shift+B`，然后👉😁😜🌴），所以 `MySQL`的表和数据库的字符集要设置为`utf8mb4`
- 由于文章、标签和分类有对应关系，所以我在数据库中设置了触发器。
    - 当标签被删除时，对应的文章被转入一个叫“其他”的标签下；分类也是一样的；
    - 文章被删除时，删除文章标签对应表中对应记录

## Node项目框架的搭建

首先，忽略业务相关的东西之后，这个项目还需要做啥？🤔

- `JWT`的生成和验证，这个是少不了的
- 错误处理和响应处理
- 参数验证
- 数据库操作的封装
- 文件目录的划分

我能想到的大概也就这么多，下面主要说文件目录的划分，怎么创建 `koa`项目，官网或者百度就可以知道，我是根据`koa-cli`创建的项目，更改的我的目录结构是这样的👇

```
myblog-backend
 ├── bin
 │   └── www // 入口
 ├── controllers  // 数据库逻辑，sql语句
 │   ├── articles.js 
 │   ├── articles_tags.js
 │   ├── article_view.js
 │   ├── category.js
 │   ├── tags.js
 │   └── user.js
 ├── middleWares //封装的中间件
 │   ├── catchError.js
 │   ├── paramCheck.js
 │   └── tokenAuth
 ├── models //一些model，用来处理响应
 │   ├── http-error.js
 │   └── http-success.js
 ├── routes //路由，这里也负责参数校验
 │   ├── article.js
 │   ├── category.js
 │   ├── tags.js
 │   └── user.js
 ├── service // 用来写业务逻辑和返回响应的
 │   ├── article.js
 │   ├── category.js
 │   ├── tags.js
 │   └── user.js
 ├── utils // 一些工具函数
 │   ├── cryp.js //MD5加密，用于登录等
 │   ├── mysql.js // 封装的mysql操作函数
 │   ├── resCode.js //响应状态码的封装
 │   └── token.js //token工具函数
 ├── app.js 
 ├── config.js //配置
 ├── package-lock.json
 ├── package.json
 └── README.md
```

## 总结

主要完成了项目的总体设计，计划准备再写响应处理、参数验证和 `token` 验证三节，数据库封装就不写了，网上很多相关教程，我用的 是`nodejs`的 `mysql`。值得注意的是由于数据库字符集为`utf8mb4`，所以在写配置的时候要注意这点要配置。
