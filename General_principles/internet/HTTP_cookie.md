## COOKIE
> cookie其实就是一个容器，用键值对存放了服务端发送给客户端的一些必要的信息，用来绕开HTTP协议无状态的特性

#### 背景
- HTTP协议是无状态的协议，这意味着服务端不会记住客户端的用户做了什么，阻碍了交互式Web应用的实现。

#### 介绍
- 服务端为了辨别用户身份而存储在客户端本地的数据。
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



#### 工作原理
- 有服务器产生内容，流


#### 特点
###### 识别功能
- 一台计算机上的多个浏览器都有各自独立存放Cookie的空间，这意味着
	1. 同一个用户使用不同的浏览器登陆某个网站时，不同浏览器记录下来的Cookie是不同的
	2. 不同用户即使使用了相同的浏览器，也不会善生相同的Cookie 

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


#### 缺陷
- 安全问题
	- Cookie以明文的形式存在于HTTP请求中

- 额外流量
	- Cookie附加在HTTP请求中，增加了流量

- 容量限制
	- Cookie本质上是一个存放键值对的容器，它的大小是有限的(4K)，对于复杂的数据，是无法保存在Cookie中的

## 应用
### 积极的应用
#### 购物商城
- 用户购物过程中，添加到购物车中的商品就是保存在Cookie中的，当用户结账的时候，服务端就会读取用户所在客户端请求中Cookie包含的商品信息。（为什么不选择使用数据库来存储，因为为数量庞大的用户选择的商品专门进行数据区的存取时，会严重拖慢服务器的响应效率）

#### 用户登陆验证
- 客户端有一个"记住密码"的选项，当用户勾选的这个选项的时候，服务端接收到客户端的这个请求之后，会根据用户的登录名和登陆密码生成一个登录凭证，存放在用户磁盘中（硬盘Cookie），当用户再次访问同一个网站的时候，只要保存在客户端硬盘中的Cookie数据没有过期，用户不需要密码就可以登录到网站

### 不安全的应用
#### 网页臭虫
- 一些公司的高层人员为了某种目的（譬如市场调研）而访问了从未去过的网站（通过搜索引擎查到的），而这些网站包含了一种叫做网页臭虫的图片，该图片透明，且只有一个像素大小（以便隐藏），它们的作用是将所有访问过此页面的计算机写入Cookie。而后，电子商务网站将读取这些Cookie信息，并寻找写入这些Cookie的网站，随即发送包含了针对这个网站的相关产品广告的垃圾邮件给这些高级人员。

#### cookie偷窃和脚本攻击
- **跨站点脚本**：
	- cookie盗贼
		- 搜集用户Cookie并发给攻击者的黑客。攻击者将利用Cookie消息通过合法手段进入用户帐户。
	- cookie投毒 
		- Cookie在储存和传回服务器期间没有被修改过，而攻击者会在Cookie送回服务器之前对其进行修改，达到自己的目的。例如，在一个购物网站的Cookie中包含了顾客应付的款项，攻击者将该值改小，达到少付款的目的。这就是Cookie投毒。

## HTTP请求与响应中的Cookie
#### 服务端发送cookie
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
			- 如下目录结构，`/test/`是cookie的path，`/test/cd`为cookie2的path。那么`/test/`下的页面就不能访问cookie2，但可以访问cookie1

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


#### 客户端发送Cookie
- 客户端通过在请求头中设置`Cookie`来将本地存储（内存&硬盘）的cookie传递给服务端。
- 注意
	- 存在与HTTP请求头中的Cookie信息只包含cookie的值（即键值对），其他选项全被去除

```
Connection:keep-alive
Cookie:name=ziawang
```




## Django中手动实现cookie验证


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
