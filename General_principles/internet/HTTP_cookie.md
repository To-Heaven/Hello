## COOKIE
> cookie其实就是一个容器，用键值对存放了服务端发送给客户端的一些必要的信息，用来绕开HTTP协议无状态的特性

#### 为什么要使用cookie？
- 根本的原因是因为HTTP协议是无状态的，Web应用中我们经常需要通过HTTP请求与Web应用程序进行交互（比如登陆验证），如果不使用cookie的话，服务端没法从每一次的请求中获取上一次请求产生的数据，服务端功能的实现可能就依赖这些数据，不利于Web应用功能的实现。有了cookie之后，我们就可以将服务端需要在每一次请求处理中使用到的数据保存到cookie中，实现服务端的功能。 

#### 介绍
- 服务端**为了辨别用户身份而存储在客户端本地的数据**。
- cookie中包含的信息
	- 电脑名
	- 用户名
	- 曾经访问过的网站
	- 浏览器信息
	- 等等

#### 表现形式
- 在客户端上，Cookie是由一组组键值对构成的，结构很像python中的字典

###### 单键值对的cookie
- `name=value`
	- 一个键对应一个值
 
###### 包含多个子键值对的cookie
- `name=k1=v1&k2=v2`
	- name还是作为cookie的键，只不过name对应的值编程了多组由`&`分隔开的键值对



#### 工作原理/流程
- 现在假设你在浏览器的地址栏中输入了`www.ziawang.com`，并点击回车，接下来整个cookie的流程就是这样
	1. 浏览器在向服务端发送请求之前，会检查本地硬盘中有没有存放有关`www.ziawang.com`站点相关的`cookie数据`，如果存在该`cookie数据`就会将所有的相关键值对存放在`请求头的Cookie对应的value中`
	2. 服务端接收到了客户端的请求之后，就可以从请求头中获取存放在客户端的`cookie`数据，进行响应的操作（比如统计新访客与旧访客），如果请求头中没有`Cookie`相关的数据，就会在响应头中通过`Set-Cookie`来向客户端发送要保存的Cookie数据（包括用户信息，sessionid，cookie的有效期等）。
	3. 当下一次客户端再次访问服务端的时候，只要`www.ziawang.com`站点存储在本地的cookie数据没有过期或者没有被删除，那么服务端就可以从请求头中获取`Cookie`数据来进行相关操作了

```python
Set-Cookie:has_counted=OK; expires=Fri, 12-Jan-2018 05:28:19 GMT; Max-Age=300; Path=/
```

#### 特点
###### 识别功能
- 一台计算机上的多个浏览器都有存放各自独立Cookie的空间，这意味着
	1. 同一个用户使用不同的浏览器登陆某个网站时，不同浏览器记录下来的Cookie是不同的
	2. 不同用户即使使用了相同的浏览器，也不会产生相同的Cookie 

###### 分块
- 同一个浏览器的Cookie访问不同的站点会携带不同站点对应的Cookie。

###### 同源策略
- **只有同源的网页才能共享同一个站点的cookie**

###### 明文保存
- 键值对都是以明文保存在浏览器cookie中，因此服务端为了保存数据的安全性，应该将要保存在cookie中的数据加密之后再保存在cookie中。

#### cookie的分类
###### 按存储位置分
- 内存Cookie
	- 特点
		- 存在时间短暂
		- 由浏览器维护，浏览器关闭之后就会消失
- 硬盘Cookie
	- 特点
		- 存在时间较长
		- 用户手动清理或者到了过期时间，硬盘中的Cookie才会被删除

###### 按存在时间
- 非持久Cookie
- 持久Cookie

#### 优点
- 数据存放在客户端，减轻服务端压力 

#### 缺陷
- 安全问题
	- Cookie以明文的形式存在于HTTP请求中

- 额外流量
	- 发送请求时，cookie会保存在请求头中，增加了流量

- 容量限制
	- Cookie本质上是一个存放键值对的容器，它的大小是有限的(4K)，对于复杂的数据，是无法保存在Cookie中的

#### 积极应用
###### 购物商城
- 用户购物过程中，添加到购物车中的商品就可以保存在Cookie中的，当用户结账的时候，服务端就会读取用户所在客户端请求中Cookie包含的商品信息。（为什么不选择使用数据库来存储，因为如果使用数据库来存放用户购物车数据，当数量庞大的用户对自己购物车中的商品进行频繁的`添加、删除、修改`操作时，对应数据库就要出现大量频繁的`访问、读、写`操作，严重拖慢服务器的响应效率性能）

###### 用户登陆验证
- 客户端有一个"记住密码"的选项，当用户勾选的这个选项，服务端接收到客户端的这个请求之后，会**根据用户的登录名和登陆密码生成一个登录凭证**，并将该`登录凭证`存放在客户端本地的`cookie`中，当用户再次访问同一个网站的时候，只要保存在客户端硬盘中的Cookie数据没有过期，程序会首先在客户端cookie中查找是否存在匹配的登陆凭证，如果该存在，用户就可以不用登陆就直接访问网站，用户不需要密码就可以登录到网站

- 举例

```
# 用户登录过程一般有三次请求


# 第一次登陆，GET请求，获取login页面
www.jd.com/login.html  GET 				#	还没有Cookie  

# 用户提交账号和密码
www.jd.com/login.html  POST user pwd	# 	登陆成功，客户端收到服务端发送来的Cookie

# 登陆成功重定向至用户home
www.jd.com/user/home.html  redirect		# 	客户端浏览器Cookie中存在了该网站返回的的Cookie键值对信息
```





#### 不安全的应用
###### 网页臭虫
- 一些公司的高层人员为了某种目的（譬如市场调研）而访问了从未去过的网站（通过搜索引擎查到的），而这些网站包含了一种叫做网页臭虫的图片，该图片透明，且只有一个像素大小（以便隐藏），它们的作用是将所有访问过此页面的计算机写入Cookie。而后，电子商务网站将读取这些Cookie信息，并寻找写入这些Cookie的网站，随即发送包含了针对这个网站的相关产品广告的垃圾邮件给这些高级人员。

###### cookie偷窃和脚本攻击
- **跨站点脚本**：
	- cookie盗贼
		- 搜集用户Cookie并发给攻击者的黑客。攻击者将利用Cookie消息通过合法手段进入用户帐户。
	- cookie投毒 
		- Cookie在储存和传回服务器期间没有被修改过，而攻击者会在Cookie送回服务器之前对其进行修改，达到自己的目的。例如，在一个购物网站的Cookie中包含了顾客应付的款项，攻击者将该值改小，达到少付款的目的。这就是Cookie投毒。

#### HTTP请求与响应中的Cookie
###### 服务端发送cookie
- 服务端在响应头中通过指定`Set-Cookie`来指定发送的Cookie，浏览器将从服务端接收到的值作为对应的cookie保存。
- 格式
	- `Set-Cookie: key=value [;expires=date][;domain=domain][;path=path][;secure]`
		- key即cookie中的键
		- value即cookie中的值
		- expires过期时间（HTTP控制缓存的基本手段）
		- domain是cookie所在的域，默认为请求的地址
			- 如果网址是`www.ziawang.com/python/index.html`，那么domain就是`www.ziawang.com`
		- path cookie所在的目录
			- ASP.net默认为`/`，即根目录
			- 浏览器会将daomain和path都相同的cookie保存在同一个文件中，并且cookie之间用`*`分隔
			- 如下目录结构，`/test/`是cookie1的path，`/test/cd`为cookie2的path。那么`/test/`下的页面就不能访问cookie2，但可以访问cookie1

```
/----
	|--test/			# cookie1
	|
	|--test/cd			# cookie2
	|
	|--test/dd			

```


- 服务端发送cookie实例

```
Connection:keep-alive
Set-Cookie:name=ziawang
```


###### 客户端携带Cookie
- 客户端通过在请求头中设置`Cookie`来将本地存储（内存&硬盘）的cookie传递给服务端。
- 注意
	- 存在与HTTP请求头中的Cookie信息只包含cookie的值（即键值对），其他选项全被去除

```
age:16723
date:Wed, 01 Nov 2017 00:52:19 GMT
etag:"59f8d886-2698"
status:304
via:1.1 varnish
x-cache:HIT
x-cache-hits:53
x-served-by:cache-jfk8126-JFK
x-timer:S1509497540.692029,VS0,VE0
Request Headers
:authority:docs.python.org
:method:GET
:path:/3/
:scheme:https
accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
accept-encoding:gzip, deflate, br
accept-language:zh-CN,zh;q=0.8
cache-control:max-age=0
cookie:__utma=32101439.632045304.1509495511.1509495511.1509495511.1; __utmc=32101439; __utmz=32101439.1509495511.1.1.utmcsr=baidu|utmccn=(organic)|utmcmd=organic
if-modified-since:Tue, 31 Oct 2017 20:09:42 GMT
if-none-match:"59f8d886-2698"
upgrade-insecure-requests:1
user-agent:Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/61.0.3163.100 Safari/537.36
```




#### Django中手动实现cookie验证


```python
from django.shortcuts import render, redirect
from app import models


def cookieAuthor(func):
    def inner(*args, **kwargs):
        is_login = args[0].COOKIES.get('loginTrue')
        if is_login:
            ret = func(*args, **kwargs)
            return ret
        else:
            return redirect('/login/')
    return inner


def login(request):
    if request.method == 'POST':
        userName = request.POST.get('username')
        password = request.POST.get('password')
        user = models.User.objects.get(name = userName)
        if user:
            if password == user.password:
                response = redirect('/manage/')
                response.set_cookie('loginTrue', 'OK', 10)
                return response
    return render(request, 'login.html')


@cookieAuthor
def manage(request):
    books = models.Book.objects.all()
    return render(request, 'manage.html', {'books': books})

```
