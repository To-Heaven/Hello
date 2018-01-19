## session


#### 为什么要用session？
- 存放在客户端本地的cookie数据是以`明文`的形式存储的，并且当客户端受到病毒入侵的时候，`cookie`中的数据很容易被病毒盗取，这是非常不安全的，因此我们不能将与用户相关的一些敏感信息保存在客户端本地。session就可以解决这个问题，当使用session之后，客户相关的敏感信息将不会保存在cookie中，而是保存在服务端（可以保存在数据库或者缓存中）。


#### 与Cookie
> session依赖cookie，因为他需要在cookie中存放将来用来获取session值的随机字符串`sessionid`

- 原先保存在客户端的重要信息保存在了服务端，服务端会返回给客户端一个键值对，客户端的Cookie存放的键值对是`{"sessionid":sessionID}`，其中sessionID是服务端返回的随机字符串，客户端再次访问服务端的时候，会携带SessionID，服务端会根据SessionID来在服务端本地的键值对`{sessionID: {key1: value1, key2: value2...}}`中找到对应的客户端信息`{key1: value1, key2: value2...}`
	- 也即是说，携带了服务端分配的sessionid的客户端才能获取保存在服务端的信息


#### Session流程分析
- 现在假设你在浏览器栏输入了`www.ziawang.com`，接下来的流程应该是这样
	1. 浏览器会从本地的cookie中寻找与`www.ziawang.com`站点相关的cookie，如果有相关的`cookie数据`，就会将这些数据放在`请求头的Cookie对应的value中`
	2. 服务端收到客户端请求之后，会从请求头中获取存放在`Cookie`中的`sessionid`，这个`sessionid`是一个服务端随机生成的32个字符的随机字符串，然后根据sessionid去`数据库或者缓存`中获取对应的`用户信息（比如用户在用户表中的id）`，如果请求头中不包含`sessionid`，那么服务端就会在`数据库或缓存`保存用户数据的同时生成一个随机字符串，这个字符串就是`sessionid`，然后通过`响应头中的Set-Cookie`将`sessionid`保存到客户端本地磁盘中
	3. 当下一次客户端访问`www.ziawang.com`的时候，就会携带包含该`sessionid`的`Cookie数据`向服务端发起请求，服务端再根据请求中`存放在cookie中的sessionid`获取用户数据

#### Session + Cookie
- Session是和Cookie一起使用的
	- 客户端的Cookie保存的是sessionid的值
	- 服务端保存的是sessionid及客户端提交的信息，以及信息的过期时间


#### Django中的Session
- 在Django中，服务端存在一张django-session表，存放了发送给客户端的sessionID（sessionKey）以及客户端POST提交的重要信息（sessionData）、以及Cookie过期时间（expire-date）
	- **客户端提交的信息会被转换成JSON对象存储在服务端**

```
# 用户登录过程一般有三次请求


# 第一次登陆，GET请求，获取login页面
www.jd.com/login.html  GET 				#	此时服务端客户端和服务端多没有session和cookie

# 用户提交账号和密码
www.jd.com/login.html  POST user pwd	# 	客户端接收到服务端返回的sessionid值存放在Cookie中，客户端提交的数据会保存在sessionid值对应的JSON对象中

# 登陆成功重定向至用户home
www.jd.com/user/home.html  redirect		# 	再次登陆时，客户端携带cookie中的sessionid，服务端根据对应的sessionid找到保存在服务端对应的客户信息。
```


