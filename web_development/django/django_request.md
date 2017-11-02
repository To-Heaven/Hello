## Django中的request请求

#### request.body
- 存放的是从客户端请求体中的bytes内容，内容的格式在请求头中的Content-Type中指出。


#### request.GET
- dict: 返回GET请求提交的数据
- GET请求只有一种传递数据的方式——提交数据附在URL后
- 特点
	- 请求体为空
	- 数据量有限制
	- 数据会暴露在地址栏中


#### request.POST

- dict: 返回POST请求提交的数据

- POST请求发送数据
	- **URL后面也可以附带数据**，并且通过`request.GET.get()`方法获取。
	- 放在请求体中，按照请求头中Content-Type的格式书写，如果格式不符合，Django就无法通过`request.POST`获取其中的数据，此时我们可以通过`request.body`获取请求体中的内容

- 注意
	- 服务器收到空的POST请求的情况也是有可能发生的（表单没有数据），因此不能使用`if request.POST:`来判断对象是否使用了POST方法，而应使用`if request.method == 'POST'`	

#### Django与wsgi.ref
- Django使用的是wsgi.ref提供的socket通信接口，本身并没有直接使用socket

###### 函数版本wsgi.ref与类版本的wsgihandler


###### WSGI协议
- Web服务器网关接口

###### uwsgi


#### Django请求生命周期
1. wsgi
2. 路由系统
3. 视图函数
4. ORM（数据库）
	- ORM从数据库获取数据后返回给视图函数
5. 模板
	- 渲染工作在Django中执行完毕后，生成字符串返回给浏览器


#### request.method
- string: 返回当前请求使用的方法

#### request.REQUEST
- 它是POST和GET属性的结合体，但是有特殊性，会先查找POST属性然后再查找GET属性。但是我觉的直接使用GET或者POST代码会更具有可读性和可维护性

#### request.path
- string: 返回URL地址中的路径("？"之前，IP地址之后的部分)
- `url(r'^$')`返回根目录`/`

#### request.user
- 当前登陆的对象(经过auth.authenticate(request, user)返回对象)
	
#### request.Keys
- 待补充
	
##### request.session
- 存放验证请求的sessionid和客户端信息
	
#### request.COOKIES
- 请求信息中的COOKIE
	
- request.FILES
	- 待补充   



类() -> __init__
类()() -> __call__