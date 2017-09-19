
## body部分常用标签
- 图形标签`<img></img>`
	- 内联标签
	- 属性
		- `src`图片路径
		- `alt`图片加载失败时的提示
		- `title`鼠标悬浮时的提示信息
		- `width`图片宽度
			- 单位：像素（1厘米大约28像素）
		- `height`图片高度
			- 单位同上  
			- 注意：
				- 当宽度和高度两个属性只用一个的时候，会自动等比缩放


- 超链接标签`<a></a>`
	- 内联标签
	- 即指向一个目标链接，该目标可以是另一个网页，也可以是本网页中的一个位置，还可以是一个图片，电子邮件地址，文件（文本文件，二进制文件），甚至是一个应用程序
	- `href`属性
		- 绝对URL：显示文件的完整路径
		- 相对URL：包含URL本身的文件夹的位置为参考点，描述目标文件夹的位置。如果目标文件与当前页面（也就是包含URL的页面）在同一个目录，那么这个文件的相对URL仅仅是文件名和扩展名，如果目标文件在当前目录的子目录中，那么它的相对URL是子目录名，后面是斜杠，然后是目标文件的文件名和扩展名。
		- 锚URL：指向页面中的锚`href="# 标签id"`
	- `target`属性
		- 对丁再何处打开链接文档
			- `_blank`在新标签中打开
			- `_self`在相同标签中打开
			- `_parent` 
			- `_top`
			- `framename`
		
- 表格标签`<table></table>`
	- 块级标签
	- `<table>`标签属性
		- `border` 指定表格边框的粗细，单位为像素，不指定的时候没有边框
		- `width` 指定表格宽度（__没有height__）
		- `cellpadding`内边距
		- `cellspacing`外边距
	- 表格的每一个单元格内可以包含文字、列表、图案、表单、数字符号、预置文本和其它的表格等内容
	- `<tr></tr>`行标签
		- 在行标签的开始标签和结束标签中可以嵌套多个数据标签`<td></td>`
	- `<td></td>`数据标签
		- 用来存放内容
		- td 常用属性
			- `rowspan`表示该单元格向右合并多少单元格
			- `cellspqn`表示该单元格向下合并多少单元格

```html
<table>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

```


- `<div><div>`
	- 块级标签
	- 该标签不带有任何附加功能和样式。
	- 主要用于用户自定义渲染样式和功能
- `<span></span>`
	- 内联标签
	- 功能和`div`标签相同，但是不是独占一行
	- 一般嵌套在最后一层

- 有序列表`<ol></ol>`
	- 列表中的每一项（行）内容用`<li></li>`标签包含
- 无序列表`<ul></ul>`   
	- 列表中的每一项（行）内容用`<li></li>`标签包含
- 定义列表`<dl></dl>`
	- `<dt></dt>` 表头
	- `<dd></dd>` 数据元素部分

```html

<dl>
<dt>Coffee</dt>
<dd>- black hot drink</dd>
<dt>Milk</dt>
<dd>- white cold drink</dd>
</dl>

```



- 表单标签`<form></form>`
	- 所有的表单都是收集用户的输入信息，实现用户与web服务器之间的交互
	- 属性
		- `action`指定服务端的URL地址
		- `method`会顶用于发送表单数据的HTTP方法
			- GET
			- POST
		- `name` 表单的名称
		- `target`在何处打开action指定的URL
			- `_blank`
			-  `self`
			-  `top`
			-  `_parent`
	- 表单中常用内嵌标签如下
		- `<input>`  定义输入域
		- `<select></select>`  定义下拉选项列表
			- `<option></option>`定义下拉列表的选项
		- `<textarea></textarea>` 定义一个文本域（文本框）
		- `<label></label>`  与input标签关联
			- for属性要与被关联input标签的id属性值相同
		- 
	- `<input></input>`标签属性
		- __input标签放在form标签内才有意义，因为服务端地址由form属性指定__
		- `name`属性
			- 为对应的input发送给服务端数据提供了键（客户端收集的数据以键值对的形式发送给服务端）
		- `value`属性
			- 为name属性指的键设定默认值，对于选择性数据要指定每一个选择对应的value 
		-  `type`属性，有以下属性值
			-  `text` 接收用户输入文本数据
			-  `password` 接收密码，密码会被隐藏
			-  `radio` 单选框，对于具有相同name属性的input标签，只能从其中选择一个，比如性别
			-  `checkbox` 复选框
				-  `checked="checked"`属性
			-  `hidden` 
			-  `file` 用于选择文件
			-  `reset` 重置form表单
			-  `<button></button>` 
				-  `<input type="button" onclick="alert('哈哈')" value="buttom">`
	- `<select></select>`
		- `name`属性应该在select标签中定义。因为内嵌在其中的option标签具有相同的键(name)。
		- `<option></option>`
			- `value`值应该在每一个option中定义，因为每一个下拉选项的值都是不同的
			- `selected `属性，默认选择该option，select有multiple时，可以给多个option设置selected
		- `muliple`当该属性为 true 时，可选择多个选项。
		- `size` 规定下拉列表中可见选项的数目。
		- `required` 规定用户在提交表单前必须选择一个下拉列表中的选项。
		- `form` 定义 select 字段所属的一个或多个表单
	- 注意：
		- 对于包含有文本框，用户自己输入数据的标签，设置的value值则为默认值，可以被用户输入的值覆盖
		- 对于选择型的标签，比如 <option>
	- `<form>`表单和HTTP协议
		- `GET`方法会将表单提交的数据挂在URL后缀上  	
			- `https://127.0.0.1:80/a/b/c?username=user&password=pass协议:https`
			- 请求的数据量受限于长度的限制，安全性差
			- 应用
				- 从数据库查询关键字
		- `POST`
			-  表单提交的数据放请求体中的在请求数据中（服务端需要从请求体中拆开取出数据）
			-  不受限于数据长度
			-  应用
				-   修改数据库的操作，增删改（注册，订单等）
	





- 标题标签`<hn></hn>`
	- 块级标签
	- n(1~6)
	
- 段落标签`<p></p>`
	- 块级标签
	- 被其包裹的内容会换行
	-  上下内容之间存在一行空白
- 加粗标签`<b></b>` 或者  ` <strong><strong>`
- 斜体标签`<em></em>`
- 角标标签
	- 上角标`<sup></sup>`
	- 下角标`<sub></sub>`
-分割线（水平线）`<hr>`
- 换行符 `<br>`
- 文字加中线`<strike></strike>`
- 特殊字符

## http协议请求头

```
请求协议：
请求首行
请求头  （chrome浏览器： 检查-->Networks--->all--->选定过程）
换行
请求数据
# ------------------------------------------------
URL: 
    https://127.0.0.1:80/a/b/c?username=user&password=pass
    协议:https
    ip地址和端口：127.0.0.1:80
    url的路径/a/b/c
    数据：username=user&password=pass
```

## URL统一资源定位符与URI统一资源标识符
- URL是URI的子集
- URI是用来标识某一互联网资源名称的字符串
- 区别
	- URI 和 URL 都定义了 what the resource is。URL 还定义了 how to get the resource。
	
## 绝对URL地址和相对URL地址
- 绝对URL：显示文件的完整路径
- 相对URL：包含URL本身的文件夹的位置为参考点，描述目标文件夹的位置。如果目标文件与当前页面（也就是包含URL的页面）在同一个目录，那么这个文件的相对URL仅仅是文件名和扩展名，如果目标文件在当前目录的子目录中，那么它的相对URL是子目录名，后面是斜杠，然后是目标文件的文件名和扩展名。
- 注意
	- 对于同一台服务器上的文件，应该总是使用**相对URL**这样更方便输入，并且在将页面从本地转移到服务器时更方便，只要每个文件的相对位置不变，链接就仍然是有效的
