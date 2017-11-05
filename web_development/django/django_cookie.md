## Django Cookie


#### 查看cookie
- 查看cookie是通过客户端的请求来获取的


###### 通过属性查看
- `value = request.COOKIES[key]`



#### 设置cookie

- Cookie的设置是通过服务端返回的响应设置的，服务端将要返回给客户端的cookie信息保存在响应头的`Set-Cookie`中

###### response.set_cookie(key, value="", max_age=None, expires=None, path="/", domain=None, secure=False, httponly=False)
- parameters
	- key: 要设置在客户端的cookie的键
	- value: 要设置对应key的value
	- max_age: cookie存在的时间，单位为秒
	- expires: 也是cookie存在的时间，不过类型是一个datetime对象
	- path: 默认为根目录。
		- 客户端在指定的路径上才能拿到Cookie，在其他路径上不会拿到对应的Cookie
	- domain: cookie生效的域名。
		- 如果该参数设置为 None ，cookie只能由设置它的站点读取
		- `domain=".example.com"`，对应的Cookie将在以下站点生效
			- www.example.com
			- xxx.example.com
			- 等等
	- secure=False
		- 设置为True的时候，浏览器将通过HTTPS来回传cookie
	- httponly=False
		- 只能http协议传输，无法被JavaScript获取（底层抓包可以获取到也可以被覆盖）
		- 待补充

###### 设置加密Cookie


#### 删除cookie
- 删除cookie也是通过服务端的响应response来实现的

###### response.delete_cookie(str_key, path="/", domain=name)
