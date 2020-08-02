# 用户管理模块

## 用户管理静态页面

+ [表格组件](http://v3.bootcss.com/css/#tables) 

+ table-bordered 添加表格边框

+ table-hover 添加hover效果

  ```html
  <table class="table table-bordered table-hover">
    ...
  </table>
  ```

+ 禁用按钮

```html
<button class="btn btn-danger">禁用</button>
```

+ 表格里面设置水平垂直居中

```css
.table.table-bordered td {
  text-align: center;
  vertical-align: middle;
}
```

+ 渲染用户列表

思路：

```
//发送ajax请求，获取到用户的数据
//结合模板引擎，把数据渲染到页面
```

```javascript
  var currentPage = 1;//记录当前页
  var pageSize = 10;//记录每页的数量

  $.ajax({
    type:"get",
    url:"/user/queryUser",
    data:{
      page : currentPage,
      pageSize:pageSize
    },
    success:function (data) {
      console.log(data);
      var html = template("tpl", data);
      $("tbody").html(html);
    }
  })
```

页面渲染

```html
<script type="text/html" id="tpl">
  {{each rows v i}}
  <tr>
    <td>{{i+1}}</td>
    <td>{{v.username}}</td>
    <td>{{v.mobile}}</td>
    <td>{{v.isDelete===1?'正常':'禁用'}}</td>
    <td>
      {{if v.isDelete === 1}}
      <button class="btn btn-danger btn-sm">禁用</button>
      {{/if}}
      {{if v.isDelete === 0}}
      <button class="btn btn-success btn-sm">启用</button>
      {{/if}}
    </td>
  </tr>
  {{/each}}
</script>
```



## 分页插件bootstrap-paginator的使用

+ 引包

```
<script src="lib/bootstrap-paginator/bootstrap-paginator.js"></script>
```

+ 页面结构

```html
<!--boostrap分页插件如果是3.0版本，要求这个盒子必须是ul-->
<div class="text-right">
  <ul id="paginator"></ul>
</div>
```

+ 初始化分页插件

```javascript
//渲染分页
$("#paginator").bootstrapPaginator({
  bootstrapMajorVersion:3,//指定bootstrap的版本，如果是3，必须指定
  currentPage:currentPage,//指定当前页
  totalPages:Math.ceil(data.total/pageSize),//指定总页数
  onPageClicked:function (a,b,c, page) {
    //page指的是点击的页码,修改了当前页
    currentPage = page;
    //重新渲染
    render();
  }
});
```

+ 修复序号的bug

因为 i 每次渲染都是从0开始

```javascript
<td> {{ (page-1) * size + (i+1) }} </td>
```



## 用户启用禁用功能

+ 列表渲染时，需要把用户的id存起来

```
<td data-id="{{v.id}}">
  {{if v.isDelete === 1}}
  	<button class="btn btn-danger btn-sm">禁用</button>
  {{/if}}
  {{if v.isDelete === 0}}
  	<button class="btn btn-success btn-sm">启用</button>
  {{/if}}
</td>
```

+ 准备弹出模态框
+ 点击禁用或者启用时
  + 弹出模态框
  + 获取到自定义属性id
  + 获取到禁用还是启用的状态（根据btn的类名）
+ 点击确定按钮时，需要发送ajax请求
  + 成功的时候，关闭模态框，重新渲染页面。

    ​



# 一级分类模块

## 分页渲染功能

+ 准备一级分类的静态页面
  + 修改了表格
+ 发送ajax，获取一级分类的数据
  + 通过模版引擎渲染到页面中
+ 分页功能
  + 准备分页的盒子

  + 引入分页的文件

  + 初始化分页控件`bootstrapPaginator`

    ​

## 添加功能

+ 准备添加的模态框

  + 准备form组件
  + 问题：表单提交不了，type=submit, 按钮没有在form表单中，所以提交不了。

+ 给表单增加校验功能

  + 引入文件
  + 初始化表单校验`$form.bootstrapValidator({ feedbackIcons:{}, fields:{} })`

  ```
  $form.bootstrapValidator({
    //小图标
    feedbackIcons: {
      valid: 'glyphicon glyphicon-ok',
      invalid: 'glyphicon glyphicon-remove',
      validating: 'glyphicon glyphicon-refresh'
    },
    //校验规则
    fields:{
      categoryName:{

        validators:{
          //非空
          notEmpty:{
            message:"请输入一级分类"
          }
        }

      }
    }
  });
  ```

  + 给表单注册一个校验成功事件

  ```
  $form.on("success.form.bv", function (e) {
    //阻止默认提交
    e.preventDefault();

    //使用ajax进行提交
  })
  ```

  + 发送ajax请求，添加一级分类

    + 关闭模态框
    + 重新渲染第一页（因为添加的数据在第一页）
    + 重置表单的数据和样式

    ```
    $form.data("bootstrapValidator").resetForm(true);
    ```

    ​

# 二级分类模块

## 准备静态页面

跟一级基本类似



## 分页渲染

跟一级基本类似



## 添加分类

+ 准备添加分类的模态框
  + 下拉菜单：http://v3.bootcss.com/components/#dropdowns

  + 使用ajax获取到所有的一级分类的数据，渲染到下拉框菜单

  + 点击下拉菜单，需要修改按钮的内容

    ​



### 文件上传（jquery fileupload插件）

+ 引包

```
<script src="lib/jquery/jquery.min.js"></script>
<script src="lib/jquery-fileupload/jquery.ui.widget.js"></script>
<script src="lib/jquery-fileupload/jquery.fileupload.js"></script>
```

+ html结构

```html
<!--
1. type:file
2. 必须指定name属性，因为后台通过这个name属性去获取图片
3. 必须data-url：指定图片上传的地址
-->
<input type="file" id="fileupload" name="pic1" data-url="/category/addSecondCategoryPic">
```

+ 使用js初始化文件上传

```javascript
//初始化文件上传
$("#fileupload").fileupload({
  dataType:"json",
  //文件上传完成时，会执行的回调函数，通过这个函数就能获取到图片的地址
  //第二个参数就有上传的结果 data.result
  done:function (e, data) {
    //console.log("图片上传完成拉");
    //console.log(data);
    //console.log(data.result.picAddr);
    $(".img_box img").attr("src", data.result.picAddr);
  }
});
```

如果有上传报错的问题：

`在public文件夹下面创建一个upload文件夹，  在upload文件夹下面创建一个brand文件夹，用于存放上传的品牌的图片的`

`在public文件夹下面创建一个upload文件夹，  在upload文件夹下面创建一个product文件夹，用于存放上传的商品的图片的`



### 隐藏域

```html
<input type="hidden" id="categoryId" name="categoryId">
<input type="hidden" name="brandLogo" id="brandLogo">
```

js代码

+ 选择下拉框时，把选中a的id存到categoryId中的value值
+ 图片上传时，把img的地址存到brandLogo中的value值



点击上传时，能够把数据传送服务端。



## 表单校验

+ 初始化表单校验-跟前面的一样
+ 问题1：隐藏的表单没有做校验`excluded:[],`
+ 问题2：隐藏域在选择的时候，并没有变成校验成功
  + 需要在下拉选择器的时候，通过js动态的改成校验成功

```
$form.data("bootstrapValidator").updateStatus("categoryId", "VALID");
$form.data("bootstrapValidator").updateStatus("brandLogo", "VALID");
```



## 商品添加功能

+ 注册校验成功事件
+ 阻止默认提交，使用ajax提交
+ ajax成功后
  + 关闭模态框
  + 重新渲染第一页
  + 重置模态框

