# HTML 学习总结（2）

# 表格
## 创建表格
```
	<table>
		<tr>
			<td>单元格</td>
			<td>单元格</td>
			<td>单元格</td>
		</tr>	
		<tr>
			<td>单元格</td>
			<td>单元格</td>
			<td>单元格</td>
		</tr>	
		<tr>
			<td>单元格</td>
			<td>单元格</td>
			<td>单元格</td>
		</tr>		
	</table>
```
1. 表格至少有三个基本的标签构成 table 代表一个表格 tr代表行 td代表单元格
2. tr必须嵌套在table标签中，td必须嵌套在tr或者th中
3. 有几个单元格就代表有几列 
    **注意：**
1. <tr></tr>中只能嵌套<td></td>
2. <td></td>标签，他就像一个容器，可以容纳所有的元素
## 表格属性
```
width  表格的宽度 （了解）
height 表格的高度 （了解）
border 表格的边框 （了解）
align  表格的对齐方式 （了解）
cellspacing 单元格与单元格的间距
cellpadding	单元格与单元格内容的间距
```
## 表格的表头标签
> 在合适的位置将td替换成th即可  th标签相对td来说更有语义性 并且会将内部的文字加粗且居中 表头一般位于表格的第一行或第一列
```
	<tr>
		<th>表头1</th>
		<th>表头2</th>
		<th>表头3</th>
	</tr>
```
## 表格的结构
```html
	<thead>
		<tr>
			<th>姓名</th>
			<th>年纪</th>
			<th>性别</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>小明</td>
			<td>18</td>
			<td>男</td>
		</tr>
		<tr>
			<td>小强</td>
			<td>18</td>
			<td>女</td>
		</tr>
	</tbody>
	<!-- <thead></thead>：用于定义表格的头部。必须位于<table></table> 标签中，一般包含网页的logo和导航等头部信息。
       <tbody></tbody>：用于定义表格的主体。位于<table></table>标签中，一般包含网页中除头部和底部之外的其他内容。 -->
```
## 合并单元格
跨行合并 rowspan 上下合并  将rowspan写在上面的单元格上  
跨列合并 colspan 左右合并  将colspan写在左边的单元格上
需要合并几个 这个值就是几  一旦合并了多余的单元格需要删掉多余的单元格
## 表格总结
1. 表格提供了HTML 中定义表格式数据的方法。
2. 表格中由行中的单元格组成。
3. 表格中没有列元素，列的个数取决于行的单元格个数。
4. 表格不要纠结于外观，那是CSS 的作用。
# 表单
作用：收集用户信息发送给后台
三大组成部分：
1. 表单域  （将内部包含的表单信息都收集起来发送给后台）
2. 提示文本（提示用户当前表单的输入内容）
3. 表单 （真正用来收集用户信息）
## 表单的分类
```
	<input type="">
	type的取值：
	text 单行文本输入框
	password 密码框
	radio 单选框（在多个里面选择一个） 单选框要生效必须具备name属性 并且同一种类型的单选框的name取值必须一样
	checkbox 复选框（在多个里面选择某几个）
	button 普通按钮
	file 用户上传控件
	submit 提交按钮
	value属性：表单提交的值，radio和checkbox需添加
	checked：默认选中项，针对radio和checkbox
```
![1567688454085](_v_images/20200313150302323_32258.png)

##  label标签
label 标签为 input 元素定义标注（标签）。
作用：  用于绑定一个表单元素, 当点击label标签的时候, 被绑定的表单元素就会获得输入焦点
for 属性规定 label 与哪个表单元素绑定。
```html
<label for="male">Male</label>
<input type="radio" name="sex" id="male" value="male">
```
## textarea控件(文本域)
如果需要输入大量的信息，就需要用到&lt;textarea&gt;&lt;/textarea&gt;标签。通过textarea控件可以轻松地创建多行文本输入框
```html
<textarea cols="每行中的字符数" rows="显示的行数">
  文本内容
</textarea>
```
## 下拉菜单
```html
<select>
  <option>选项1</option>
  <option>选项2</option>
  <option>选项3</option>
  ...
</select>
```
**注意：**
1. &lt;select&gt;</select&gt;中至少应包含一对&lt;option></option&gt;。
2. 在option 中定义selected =" selected "时，当前项即为默认选中项。
## 表单域
在HTML中，form标签被用于定义表单域，即创建一个表单，以实现用户信息的收集和传递，form中的所有内容都会被提交给服务器。
```html
<form action="url地址" method="提交方式" name="表单名称">
  各种表单控件
</form>
```
常用属性：
1. Action
   在表单收集到信息后，需要将信息传递给服务器进行处理，action属性用于指定接收并处理表单数据的服务器程序的url地址。
2. method
   用于设置表单数据的提交方式，其取值为get或post。
3. name
   用于指定表单的名称，以区分同一个页面中的多个表单。
**注意：  每个表单都应该有自己表单域。**
# HTML5新标签与特性
## 文档类型设定
- document
  - HTML:
  - XHTML:
  - HTML5
## 字符设定
- <meta http-equiv="charset" content="utf-8">：HTML与XHTML中建议这样去写
- <meta charset="utf-8">：HTML5的标签中建议这样去写
## 常用新标签
- header：定义文档的页眉
- nav：定义导航链接的部分
- footer：定义文档或节的页脚
- article：标签规定独立的自包含内容
- section：定义文档中的节（section、区段）
- aside：定义其所处内容之外的内容
## 常用新属性
|     **属性******     |                 **用法******                  |               **含义******               |
| -------------------- | --------------------------------------------- | ---------------------------------------- |
| **placeholder******  | <input type="text" placeholder="请输入用户名"> | 占位符提供可描述输入字段预期值的提示信息    |
| **autofocus******    | <input type="text" autofocus>                 | 规定当页面加载时 input 元素应该自动获得焦点 |
| **multiple******     | <input type="file" multiple>                  | 多文件上传                                |
| **autocomplete****** | <input type="text" autocomplete="off">        | 规定表单是否应该启用自动完成功能            |
| **required******     | <input type="text" required>                  | 必填项                                   |
| **accesskey******    | <input type="text" accesskey="s">             | 规定激活（使元素获得焦点）元素的快捷键       |
## 新增的type属性值：

|   **类型******   |    **使用示例******     |    **含义******    |
| ---------------- | ----------------------- | ------------------ |
| **email******    | <input type="email">    | 输入邮箱格式        |
| **tel******      | <input type="tel">      | 输入手机号码格式     |
| **url******      | <input type="url">      | 输入url格式         |
| **number******   | <input type="number">   | 输入数字格式        |
| **search******   | <input type="search">   | 搜索框（体现语义化） |
| **range******    | <input type="range">    | 自由拖动滑块        |
| **time******     | <input type="time">     |                    |
| **date******     | <input type="date">     |                    |
| **datetime****** | <input type="datetime"> |                    |
| **month******    | <input type="month">    |                    |
| **week******     | <input type="week">     |                    |
| **color******    | <input type="color">    | 颜色                |
## fieldset和datalist
```html
<fieldset>
    <legend>名字</legend>
    <input type="text" name="num" id="num" list="cList" placeholder="请选择"><br>
    <datalist id="cList">
      <option value="1"></option>
      <option value="2"></option>
      <option value="3"></option>
    </datalist>
  </fieldset>
```
# 多媒体标签
## 视频
网页中插入视频有两种方法
1. 没有兼容性的 将视频文件上传到第三方网站获取其分享代码放到自己的页面中即可
	+ 优点：没有兼容性
	+ 缺点：有广告植入
2. 使用H5新增的video方法  
```html
<video src="视频路径">	
</video>
```
+ 优点：没有广告
+ 缺点：有兼容性，一般运用在手机端
video标签的常用属性
1. autoplay  自动播放
2. controls  播放控件
3. loop 	循环播放
video的格式支持
ogg,MP4,webm 不同的浏览器支持的格式不一样，所以出现了一种兼容写法:前提是准备三种格式的视频文件
```html
<video autoplay>
	<source src="视频1.ogg">
	<source src="视频1.mp4">
	<source src="视频1.webm">
	<a href="#">你的浏览器不支持video，点击升级吧</a>
</video>
```
浏览器会从上到下依次去读，在这个过程中，只要读到自己识别的视频文件就直接播放这个视频文件，并且不会再往后继续读取
实测：目前的主流浏览器对Mp4的支持都比较好！！
## 音频
音频的使用和视频的使用基本一致
```html
<audio>
	<source src="音频1.ogg">
	<source src="音频1.mp3">
	<source src="音频1.webm">
	<a href="#">你的浏览器不支持audio，点击升级吧</a>
</audio>
```
audio标签的常用属性
1.autoplay  自动播放
2.controls  播放控件
3.loop 	循环播放
## 多媒体 embed
embed可以用来插入各种多媒体，格式可以是 Midi、Wav、AIFF、AU、MP3等等。url为音频或视频文件及其路径，可以是相对路径或绝对路径。
兼容性问题，插入网络视频 
```html
<embed src="http://player.youku.com/player.php/sid/XMTI4MzM2MDIwOA==/v.swf" allowFullScreen="true" quality="high" width="480" height="400" align="middle" allowScriptAccess="always" type="application/x-shockwave-flash"></embed>
```