# jQuery操作Cookie

## 配置
- 要想在script中使用jQuery操作cookie，需要导入jQuery的`jquery.cookie.js`文件，一般都是使用CDN导入

```
https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.js
https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.min.js
```

## 常用操作
#### 创建一个cookie
###### 1. 创建会话cookie
- 所谓会话cookie，其实就是其存在时间比较短，当浏览器被关闭的时候，存放的cookie就会被清除，**创建会话cookie的时候，我们不需要指定cookie的存在时间，默认创建的就是会话cookie**

- `$.cookie('key', 'value')`

###### 2. 指定存活时间
- 在声明了`key`，和`value`之后，使用一个**js对象来存放其他的参数**
- `$.cookie('key', 'value', {expires: int_days})`

###### 3. 指定cookie的有效路径
- 有效路径在我们开发的过程中几乎不用。默认情况下该参数的值为创建cookie的网页所在的路径，如果我们想要在整个网站的网页中都能够访问这个cookie，就需要把路径设置为`/`
- 在默认情况下，只有设置了cookie的网页才能读取cookie
- `$.cookie('key', 'value', {expires: int_days, path: str_path})`

###### 其他参数
- secure: 当为true的时候，cookie传输需要使用https协议，默认false
- raw: 当为True的时候，读取和写入cookie的时候**不进行编码和解码**
	- 编码: encodeURIComponent
	- 解码: decodeURIComponent

#### 读取cookie
- 读取cookie中存放的值时，只需要传入一个key作为参数即可
	- 如果对饮更的value存在，就返回该值
	- 如果对应的value不存在，就返回一个**null**
- `$.cookie('key')`


#### 删除cookie
- 删除cookie很简单，只需要将key对应的value设置成null即可
- `$.cookie(key, null)`

#### 修改cookie
- 由上面可以知道，修改一个cookie可以直接通过设置key对饮更的value实现
- `$.cookie('key', 'new_value')`

## 使用

- 操作cookie很简单，只需要`$.cookie(str_key)`即可获取对应的值




#### 获取csrftoken数据
- 在Web开发中，前后端使用POST请求交互的时候，前端发送的请求必须要通过csrf的验证才能提交数据给服务端，使用jQuery也可以获取客户端浏览器cookie中保存的csrf随机字符串

```
<script src"https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.min.js"></script>

<script>
$.ajax({
	headers: {"X-CSRFToken": $.cookie("csrftoken")}		// 从`jquery.cookie.js`文件中获取"csrftoken"
})

</script>
```


#### 获取客户端保存的临时信息
- 比如游客要对网站文章进行评论时，我们会将其页面重定向到登陆界面，并将重定向之前页面的url保存在cookie中，当用户登陆完成之后，就可以从cookie中获取路径信息并实现回到评论页面的效果

