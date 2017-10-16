# jQuery
- 什么是jQuery
	- 类似python中的模块，它是JavaScript的一个库
- jQuery优点
	- 短小精悍，便于快速开发
- 版本
	- 1.X
		- 兼容落后的IE浏览器
	- 3.X
		- 最新版，不兼容IE
- `min.xx`
	- 压缩版，生产环境使用
	- 节省网络带宽 
- 使用
	1.使用`<script>`导入
		- `<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.js"></script>`

- 注意引用顺序
	- 在HTML中要

- 关于`$`符号
	- jQuery中的`$`符号其实是jQuery对象的别名，两者都是一个函数
	- jQuery把`$`变量名占用为其别名，如果程序中`$`被其他对象占用了，那么就需要使用`jQuery.noConflict()`方法将jQuery的别名`$`交出。之后使用jQuery方法的时候，就只能用jQuery代替`$`
	




## jQuery对象和DOM对象之间的转换
> jQuery对象不能调用DOM对象的方法

- 将jQuery对象转换成DOM对象
	- jQuery是一个数组，里面存放着DOM对象和一个 `prevObject`对象
	- 取出指定的DOM对象	
		- 使用数组的索引操作
		- 使用jQuery对象的get(index)方法

- 将DOM对象转换成jQuery对象
	- 使用`$(DOM_obj)` 