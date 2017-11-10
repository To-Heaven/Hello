## session
> Session





#### 与Cookie
- 原先保存在客户端的重要信息保存在了服务端，服务端会返回给客户端一个键值对，客户端的Cookie存放的键值对是`{"sessionid":sessionID}`，其中sessionID是服务端返回的随机字符串，客户端再次访问服务端的时候，会携带SessionID，服务端会根据SessionID来在服务端本地的键值对`{sessionID: {key1: value1, key2: value2...}}`中找到对应的客户端信息`{key1: value1, key2: value2...}`
	- 也即是说，携带了服务端分配的sessionid的客户端才能获取保存在服务端的信息


#### Session + Cookie
- Session是和Cookie一起使用的
	- 客户端的Cookie保存的是sessionid的值
	- 服务端保存的是sessionid及客户端提交的信息，以及信息的过期时间


## Django中的Session
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


