# 第二天笔记
## 学习目标
1. 表格的创建和使用
2. 常见表单的使用
3. 了解HTML5新增的标签及其表单属性 

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

### 表格属性
```
width  表格的宽度 （了解）
height 表格的高度 （了解）
border 表格的边框 （了解）
align  表格的对齐方式 （了解）
cellspacing 单元格与单元格的间距
cellpadding	单元格与单元格内容的间距
```
### 表格的表头标签
> 在合适的位置将td替换成th即可  th标签相对td来说更有语义性 并且会将内部的文字加粗且居中
```
	<tr>
		<th>表头1</th>
		<th>表头2</th>
		<th>表头3</th>
	</tr>
```

### 表格的结构
```
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
```
表格的结构不是一定需要的，但是添加上表格的结构会使表格的语义更明确

### 合并单元格

跨行合并 rowspan 上下合并  将rowspan写在上面的单元格上  
跨列合并 colspan 左右合并  将colspan写在左边的单元格上

需要合并几个 这个值就是几  一旦合并了多余的单元格需要删掉多余的单元格

## 表单
作用：收集用户信息发送给后台
三大组成部分：
1. 表单域  （将内部包含的表单信息都收集起来发送给后台）
2. 提示文本（提示用户当前表单的输入内容）
3. 表单 （真正用来收集用户信息）
<img src="media/bd.png" height="800" width="800" alt="">

### 表单的分类
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
### 其他表单的补充

```
textarea 多行文本输入框
select 下拉菜单配合option
```

### 表单补充

```
radio和checkbox 默认选中项  checked
select框的默认选中项 selected
label标签的使用
```

### 表单域

	<form action=""></form>

	action 提交的后台地址
	method 提交的方法
```
表单：
三个方面：表单域  提示文本 表单


表单域：收集表单域内部的表单信息提交给后台
action属性 ：提交的后台地址
配合submit提交按钮 
表单想要被提交 需要name属性

提示文本：提示用户表单是干什么用的  本质上和表单没有关系

表单：

input分类：
	text  单行文本输入框
	password  密码框
	radio  单选框
	checkbox  复选框（多选框）
	file     文件上传控件
	submit  提交按钮
	button 普通按钮

button  默认类型是submit  有兼容问题
select  下拉框  配合option
textarea  文本域  （多行文本输入框）


表单的属性：
value 表单内部包含的值 
name  表单的名字  配合form表单提交信息
checked  默认选中项  针对的是radio和chekcbox
selected  默认选中项  针对的是select  写在option


label标签：将表单和文字关联起来  点击文字就相当与点击了表单

```
## H5新增的标签及其表单属性
```
容器标签： div  span 没有任何的语义性 就是一个容器

```
### H5新增的标签
```
header,nav,section,footer,aside,article  

这些新增的标签不会带来任何视觉效果的改变，它的作用仅是增加 语义性，这些标签有兼容问题，IE6 7 8 后续有方法兼容

header 表示header里面包裹的东西是网站的头部区域

nav 表示nav里面包裹的东西是网站的导航

section 表示里面包裹的东西是网页的某一个模块

footet 表示footer标签里面包裹的东西是网页的页脚

aside 表示aside标签里面包裹的东西是网页的侧边栏

article 表示article标签里面包裹的东西是网页的文章页

详细请查阅文档
```
### H5新增的表单新属性

1. placeholder 占位文本  placeholder 占位文本 用来提示用户当前表单应该输入的内容 和value有本质不同 value是表单包含的值 会被提交给后台 而placeholder里面的值永远不会提交 
2. autofocus   自动获取焦点 
3. autocomplete   自动补全功能  前提是该字段必须成功提交过一次   on是默认值 自动开始该功能 off 关闭该功能
	 <input type="text" autocomplete="on" name="username"> 
4. required    自动验证表单 自动验证表单不能为空
### H5新增的表单
```
滑块：<input type="range">
	这个日期表单在手机端可以使用 
日期：<input type="date">
	在手机端可以唤醒数字键盘
数字：<input type="number">
颜色：<input type="color">
	详细的请查阅文档
```
### 多媒体标签

#### 视频

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

#### 音频

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

### 知识补充

#### 字符集的设置

字符集的核心点就是如果设置了以什么字符集进行读取，那么在保存的时候也需要设置成对应的字符集

utf-8 收录了全世界所有国家的语言文字
gbk 收录的是汉字

utf-8 > gbk  所以查询来说的话gbk的查询和存储都小于utf-8
但是现在国际主流都是utf-8，所以为了后期的考虑，都用utf-8


