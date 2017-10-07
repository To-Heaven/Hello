# 常用浏览器对象
- JavaScript内置了浏览器对象（全局变量），可以通过这些对象操作浏览器、页面的信息和内容


## window
- window对象用来操作当前浏览器（当前标签页/窗口）。
- 常用属性
	- `window.innerWidth`
		- 当前网页占用的宽度
	- `window.innerHeight`
		- 当前网页占用的高度
	- `outerWidth`
		- 浏览器的整个宽度
	- `outerHeight`
		- 浏览器的整个高度  


## navigate
- navigate对象用来获取浏览器及计算机的相关信息
- 常用属性
	- `navugate.appName`浏览器名称
	- `navugate.appVersion`浏览器版本
	- `navugate.language`浏览器使用的怨言
	- `navugate.platform`浏览器运行所在的操作系统
	- `navugate.userAgent` 浏览器设置的`User-Agent`字符串


## document
- HTML页面在浏览器看来就是DOM树形结构，document是DOM树的根节点，它代表整个页面
- 可以通过document对象来对页面中的单个或者一组标签进行操作
- 常用属性
	- `document.title`
		- 用来设置页面标题
	- `document.cookie`
		- 存放一个个键值对
		- HTTP是无状态的，每一次客户都按发送来的请求信息（状态）都不会被服务端保存，cookie就是用来保存客户都按request中必要的信息（比如用户名，密码等）。客户端认证登陆成功之后，服务端会返回一个cookie给客户端（浏览器），之后浏览器再次访问服务端的时候，会将cookie放在request中，这样服务端就能识别出客户端的身份了 
