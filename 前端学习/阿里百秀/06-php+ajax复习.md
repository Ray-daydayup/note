# PHP+AJAX 复习

### 1-网络相关常识 和基本概念

ip地址:   计算机在网络中的唯一标识,      就是用来定位计算机的      缺点: 不好记

域名:   其实就相当于给 ip 地址起了个名字

DNS:   记录了 ip 和 域名的对应关系

本地hosts:    也可以记录 ip 和域名的对应关系,  优先级高

端口:  用来区分应用程序, 不同的端口, 对应不同的服务



面试判断题:  域名 和  IP地址 一定是 一对一 的关系么 ?   ( 不一定 )

域名 和 ip 可能是 多对一,   多个域名指向同一个 ip地址

域名 和 ip 也可能是 一对多,   通过域名解析得到一个ip地址(主服务器),  通过主服务器进行分发到其他分服务器 



### 2-表单提交  (get, post)

+   前端页面：
    +  action :    指定提交的地址
    +  method :   指定提交方式  (get/post)
    +  name： 指定给表单元素, 将来后台通过 name 来获取数据
    +  enctype:  文件上传, 指定值 :  multipart/form-data


+   后面处理表单常用的超全局变量
    + $_GET
    + $_POST 
    + $_FILES
    + $_SESSION      获取操作 session
    + $_COOKIE        获取 cookie, 间接的获取的cookie数据, 获取的是请求头中携带的cookie数据

### 3-HTTP协议:  规定请求和响应的标准的  

1. 请求 request 

```pjavascript
构成：
	(1) 请求行   请求地址   请求方式   协议
	(2) 请求头   系统或者浏览器对应的版本信息, 可以设置请求体的编码方式(post)
	(3) 请求体   传送给后台的参数(post)
```

2. 响应 response 

```javascript
构成:
	(1) 状态行   协议   状态码    状态文本
	(2) 响应头   服务器相关版本信息, 响应体的内容长度(用来进行内容长度校验, 防止内容丢失) ....
	(3) 响应体   响应给浏览器进行解析的信息
```


    常见状态码：
    	200  成功
    	302  重定向   header("location: 地址")
    	403  资源不可用, 资源访问受限, 请求是成功了, 但是服务器限制了, 不给返回
    	404  找不到
    	500  服务器错误
3. get请求和post请求的区别

```javascript
(1) get 请求的特点
	1. get 参数拼接在地址栏,  ?name=pp&password=123456
    2. 不太安全, 参数暴露在地址栏
    3. 大小最大 4k,  不能用于上传文件
    4. 传输速度快

(2) post请求的特点
    1. post 参数在请求体中,  需要设置请求体的编码方式, 设置请求头
       setRequestHeader('content-type', 'application/x-www-form-urlencoded');
	2. 相对安全
    3. 大小没有限制, 可以用于上传文件         可以在服务器端限制(上传文件的大小, 修改配置文件即可)
```

​	http协议的请求方式： get  post   put  delete    (早期http基于增删改查的划分方式, 了解, 现在基本只用get和post了)



#### 前端可以向后台请求的几种方式

```
常用请求方式
1、 ajax发送请求
2、 表单提交发送请求
3、 a标签的href跳转发送请求

资源型请求:
1、 link标签的href属性
2、 script标签的src属性
3、 img标签的src属性
```





需求: 找出 students 表中分数最高(排序)的前 1000个人(截取)

`select * from students order by score desc limit 0, 1000 `

### 4-sql 语法

**增删改查**
1. 插入    

```sql
insert into 表名 ( 字段1, 字段2, .... )   values  (值1, 值2, .... )
```

2. 删除    

```sql
delete from  表名 where id = 10;
```

3. 修改     

```sql
update 表名 set 字段1=值, 字段2=值2  where 条件
```

4. 查询    

```sql
select name, age, sex from 表名
```

**高级查询**

1. where 条件

```sql
并且  and     where id > 3  and  id < 10
或者  or      where id < 3  or   id > 10
等于  =       where id = 10
不等于 !=
大于小于  >  <
```

2. 统计条数 count(*)

```
select count(*) as total from stu;
```

3. 一对多匹配  in    (常用于批量操作)

```
delete from 表名 where id in (3,5,7)
```

4. 模糊匹配  like

```
找姓王的人: where name like "王%"
```

5. 表的截取  (常用于 筛选前几条,  分页)

```sql
limit 起始索引, 截取的长度
```
6. 排序   

```sql
order by 字段       默认升序
order by 字段 desc  降序

新添加的数据显示在第一条
order by id desc;
```

7. 联合查询

```sql
select 字段列表 from 表A join 表 B on 表A.字段=表B.字段  where 条件
```

### 5-PHP操作mySql 相关api

1. 连接数据库  `mysqli_connect( ip, 用户名, 密码, 数据库, 端口号 );`

2. 准备 sql(外双内单)

    双引号可以解析变量

    双引号和单引号性能, 单引号性能高, 不用解析变量

3. 执行sql   `mysqli_query( $link, $sql )`

    (1) 非查询语句,  返回 true/false

    (2) 查询语句, 成功返回结果集, 失败返回false, 

    ​     `$link 和 结果集 都属于资源型(source)对象`, 如果要从结果集中取数据, 必须要用对应的方法 `mysqli_fetch_assoc( $res )`

4. 显示错误信息  `mysqli_error( $link )`

5. 获取结果集的行数 `mysqli_num_rows( $res )`

6. 读取结果集中数据  `mysqli_fetch_assoc( $res )`

    ```
    $arr = [];
    while( $row = mysqli_fetch_assoc( $res ) ) {
      $arr[] = $row;
    }
    ```

7. 关闭数据库连接   `mysqli_close( $link )`

    ​


### 6-cookie 和 Session
1. cookie 浏览器端存储数据的容器

```;javascript
操作cookie的常用方法，及cookie的特点：
	$.cookie(k,v); 设置
	$.cookie(k)； 获取
	$.cookie(k, v , {expires: 1})  设置有效期   设置有效期为负数 就会自动删除

php 操作cookie:
	setcookie(k, v, 有效期)  设置cookie 
	$_COOKIE超全局变量 只能获取cookie 

cookie特点：
	1. 大小 4k
	2. 请求时将 cookie 中存储的数据进行携带,  携带在请求头中
	3. 默认是会话级别的, 可以设置有效期
	4. 同一个网站的多个页面可以共享数据
	5. 不同浏览器 cookie 不能共享	
```



2. session  服务器端存储数据的容器

```javascript
 操作session的常用方法，及session的特点：
 	使用之前先开启 session_start();
	$_SESSION['name'] = 'zs';
	unset($_SESSION['name']);
	$_SESSION= []; 

用户第一次访问时, session_start开启session后会做的事情：
    1. 动态生成一个 sessionId (随机字符串)
    2. 根据 sessionId 动态创建 session 文件, 可以在 session 存储数据
    3. 将 sessionId 设置在响应头里面, 返回给浏览器, 浏览器设置 cookie 存在 cookie中
```



+ cookie 和 session 配合 实现登录状态保持 的思路
```javascript
1. 在登录成功时, 记录用户信息
	session_start();
	$_SESSION['user_id'] = '12';
	$_SESSION['username'] = 'pp';
2. 下次直接从 session 中取用户数据
	session_start();
	echo $_SESSION['user_id'];
	echo $_SESSION['username'];
```



- cookie 和 session 配合 实现登陆拦截 的思路

```
判断 cookie 中有没有 sessionId
1. 如果cookie 中没有 sessionId, 直接拦截到登陆页
2. 如果cookie 中有 sessionId, 尝试获取用户信息
	(1) 如果能获取到, 认识当前用户, 啥事不用干
	(2) 如果获取不到, 拦截到登陆页
```



- 退出功能实现的思路

```
1. 服务器端销毁 session 文件即可   session_destroy();
2. 浏览器端, 清空浏览器缓存也可以清除登录状态, 清除了 cookie
```




### 7-原生Ajax(XMLhttpRequest对象)

```javascript
发送 get 请求
var xhr = new XMLHttpRequest();
xhr.open( 'get', '0.php?username=pp', true );
xhr.send( null );

发送 post 请求
var xhr = new XMLHttpRequest();
xhr.open( 'post', '0.php', true );
xhr.setRequestHeader('content-type', 'application/x-www-form-urlencoded');
xhr.send( 'username=pp&password=123456' );

xhr.onreadystatechange = function() {
  if ( xhr.readyState === 4 ) {   // 响应完成
    if ( xhr.status === 200 ) {  // 响应成功
      var result = xhr.responseText;  // 处理响应
      
    }
  }
}


```

### 8-$.ajax()

```javascript
//1-语法：
$.ajax({
  type: "get",
  url: "0.php",
  data: {
    name: 'pp',
    age: 18
  },
  dataType: 'json',     // json xml  text        jsonp
  timeout: 2000,
  beforeSend: function() { .... },  // 发送请求前调用, 里面可以进行表单校验, 如果没通过, return false;
  success: function() { ... },
  error: function() { .... },
  complete: function() { .... }    // 不管成功或者失败都会调用
}) 

//2-beforeSend如何终止请求  return false;
```





### 9-模板引擎 (template-web.js)

```javascript
使用步骤：
    1. 引包
    2. 准备模板
    3. 准备数据
    4. 将模板和数据相结合
    
    template('模板id', 数据对象);  必须是对象, 在模板中可以使用数据对象中的所有属性
语法：
	 1. 基本渲染
     	{{ name }}
		{{ age }}
     2. 遍历
     	{{ each list v i }}
		{{ /each }}
        $value $index
     3. 判断
     	{{ if 条件 }}
        {{ /if }}
        
        
        {{ if 条件 }}
         
        {{ else }}
         
        {{ /if }}
```



### 10-JSONP的原理

```
jsonp 用来解决跨域问题,  cors 也可以解决跨域问题 (有一定兼容性问题 ie10+)

jsonp 和 ajax 的关系 - 没有半毛钱关系
jsonp (json with padding)

jsonp 的原理:
	1. 利用了 script 标签可以跨域请求数据的特性, 向后台发送 get 请求
	2. 后台返回一个函数调用, 将数据作为参数传递给前端

前端:
	1. 声明一个全局函数
	2. 动态创建 script 标签, 将函数名传递给后台

后台:
	1. 接收函数名
	2. 返回函数调用, 将json数据作为参数返回
	
真实开发过程中: 前端使用 jquery 中封装好的 jsonp
	$.ajax({
      type: "get",
      dataType: 'jsonp'
      ....
	})

```



### 11-XMLHttpRequest2.0 

```javascript
//1-超时
xhr.timeout = 2000;
xhr.ontimeout = function() { ... }
//2-formData
//  可以用来管理表单数据   基本表单+文件都可以管理  
//  (1) 只能是 post 请求
//  (2) 不需要设置请求头
//  (3) 可以作为 send 的参数
//  (4) 追加 append( name, value );
var formData = new FormData( form dom 对象 );
                            
// 结合 $.ajax 使用
$.ajax({
  type: "post",
  url: "地址",
  data: formData,
  contentType: false, // 不设置请求头
  processData: false, // 不进行编码
  success: function() {
    // ....
  }
});
                            
//3-上传文件的进度 upload.onprogress
xhr.upload.onprogress = function( e ) {
  // e.loaded 表示已上传的,   e.total 全部的总大小
  var percent = (e.loaded / e.total * 100).toFix(2);
}
```

### 12-浏览器输入网址向服务器发送请求的过程
```javascript
1. 输入网址, 先进行 dns 解析, 得到 ip地址, 通过 ip 请求服务器
2. 如果请求的是静态资源文件(js, png, html), 不需要服务器进行解析, 直接返回
3. 如果请求时是php等动态文件, 会先进行服务器端的解析, 再将输出的结果, 返回给浏览器
4. 浏览器接收响应, 通过渲染引擎进行渲染
```
### 13-常用的各种插件
```javascript
1-分页： jquery.pagination.js
2-时间格式化： moment.js
  moment().format('YYYY-MM-DDTHH:mm')
3-富文本编辑器：wangEditor.js
```
### 14-其他知识
```. ;javascript
1-当用户输入的事件:  input 事件,  可以监听用户输入
  inp.oninput = function() { ... }
  
2-控制文件上传的类型：
  accept="image/png, image/gif, image/jpg"
   
3-图片本地预览：URL.createObjectURL(文件对象)
   
4-表单重置： $('#form')[0].reset();
  reset() 是 dom 对象的方法
 
5-事件委托的使用场景：
	(1) 动态渲染的元素, 需要通过事件委托注册
	(2) 给按钮, 批量注册事件, 也用事件委托
	
	$('tbody').on("click", ".btn-del", function() { .... })
  事件委托原理: 就是事件冒泡
```
### 15-增删改查的思路

```javascript
    1-添加数据思路：
		1- 先写好后台接口
		2- 前端请求接口
		3- 请求完成后重新渲染页面

		1- 点击按钮
		2- 获取数据： $('form').serialize();   var formData=new FormData();
		3- 通过ajax把数据发送给后台
		4- 后台获取数据，存到数据库中
		5- 添加完成，重新渲染页面  (渲染使用模板引擎)

	2- 删除数据思路：
		1- 获取对应数据id
		2- 通过ajax把id传给后台
		3- 后台根据id进行删除
		4- 删除成功后，重新渲染当前页

	3- 更新数据思路：
		1- 获取对应数据id
		2- 通过ajax把id传给后台
		3- 后台根据id返回对应的数据
		4- 把返回的数据渲染在页面中，供修改
		5- 修改完成，把数据根据id更新回数据库
		6- 修改成功后，重新渲染当前页

	4- 查找数据思路：
		1- 根据查找条件，向后台发送ajax请求
		2- 后台根据条件返回对应的数据
		3- 前端根据返回数据格式解析数据 xml  json
		4- 准备模板引擎，把数据渲染到页面中
        
    5-分页的思路实现过程
    	1- 获取后台数据中数据总数
        2- 根据总数生成分页标签
    
    6-实际工作中前后端分工：
    	后台 给前端提供数据和接口 （对数据进行增删改查操作）
        前端 对数据进行渲染
    	
```

