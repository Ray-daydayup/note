# XHR2 调用接口上传文件

```js
/**
 * 异步上传文件
 */
$('#upload').on('change', function () {
      // FormData用于管理表单数据的
      var  form=document.querySelector("#form1");
      //formData对象 管理form表单
      var  formData=new FormData(form);

      // 发送给服务器 
      var  xhr=new XMLHttpRequest();
      xhr.open('post','./02-formData.php');
  	  //1-必须使用post方式
  	  //2-切记不需要设置请求头，浏览器会自动设置要给合适的请求头
      xhr.send(formData); //直接发送formData 

      xhr.onreadystatechange=function(){
        if(xhr.readyState==4 && xhr.status==200){
          var r=xhr.responseText;
        }
      }
})
```

> 🚩 源代码: step-82

jQuery 也是可以的（内部仍然是使用的 XMLHttpRequest level 2）

```js
/**
 * 异步上传文件
 */
$('#upload').on('change', function () {
  // 准备要上传的数据
  var formData = new FormData(form)
  formData.append('file', this.files[0])

  // 发送 AJAX 请求，上传文件
  $.ajax({
    url: '/admin/upload.php',  
    contentType: false, //设置编码类型
    processData: false, //设置传递值方式
    data: formData,
    type: 'post',
    success: function (res) {
      if (res.success) {
        $('#image').val(res.data).siblings('.thumbnail').attr('src', res.data).fadeIn()
      } else {
        $('#image').val('').siblings('.thumbnail').fadeOut()
        notify('上传文件失败')
      }
    }
  })
})
```


1、contentType:

```
(默认: "application/x-www-form-urlencoded") 发送信息至服务器时内容编码类型。默认值适合大多数情况。如果你明确地传递了一个content-type给 $.ajax() 那么他必定会发送给服务器（即使没有数据要发送）
```

2、processData

```
(默认: true) 默认情况下，通过data选项传递进来的数据，如果是一个对象(技术上讲只要不是字符串)，都会处理转化成一个查询字符串，以配合默认内容类型 "application/x-www-form-urlencoded"。如果要发送 DOM 树信息或其它不希望转换的信息，请设置为 false。
```

3、FormData

```
XMLHttpRequest Level 2添加了一个新的接口FormData.利用FormData对象,我们可以通过JavaScript用一些键值对来模拟一系列表单控件,我们还可以使用XMLHttpRequest的send()方法来异步的提交这个"表单".比起普通的ajax,使用FormData的最大优点就是我们可以异步上传一个二进制文件.
```

