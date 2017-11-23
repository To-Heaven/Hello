# 使用JavaScript实现Ajax异步请求

## 概要
- 在面向对象设计中，要想实现或者调用一个对象的功能，首先我们要通过实例化的到这个对象。在JavaScript中，要想实现Ajax异步请求，我们需要实例化的到一个`XMLHttpRequest`对象，通过调用该对象的方法，我们可以实现下面几件事儿
	1. 创建链接
	2. 发送请求
	3. 接收响应
	4. 读取服务端响应内容
	

## 实现Ajax异步请求
#### 创建XMLHttpRequest对象
- 创建一个对象很简单，代码如下

```javascript

xmlHttp = XMLHttpRequest();

```


###### 兼容性
- 在不同的浏览器中(IE)，实例化得到XMLHttpRequest对象的方法也是不同的，因此在开发中，我们要考虑到浏览器的兼容。
- 因此我们将实例化对象的过程封装到函数中，代码如下

```javascript	
function createXMLHttpRequest(){
	
	
}
	
```

#### 使用open建立链接
- `xmlHttp.open(method, url, async=true)`
	- method: 请求方法
	- url: 请求路径
		- 对于`GET`请求，我们需要在url后面附上请求参数
	- async: 是否异步发送请求


#### 使用send发送请求
- `xmlHttp.send(request_body || null)`
	- 参数代表请求体中的内容，请求为GET请求的时候，我们必须要为该方法传入一个null

#### 接收服务器响应
###### XMLHttpRequest对象的五个状态
- 在整个异步请求的生命周期中，请求对象有五个状态，每一个状态都对应一个**整数**
	- `0`: 尚未与服务端使用`open`建立连接
	- `1`: 已经以服务端`open`建立链接，但是没有使用`send`发送请求
	- `2`: 已经发送请求，但是处于等待接收服务端响应结果的状态
	- `3`: 接收到服务端的响应结果，并使用JavaScript进行解析
	- `4`: 解析结束，断开请求

###### 接收响应
- 使用对象的`onreadystatechange`触发事件，每一次请求状态的改变，都会触发一次该事件（除了0代表的状态）
- 因此我们可以通过判断请求的状态值来指定请求处于每一个状态对应的事件。
- XMLHttpRequest对象的`readyState`属性可以获取对象当下所处的状态
- 有时我们还要通过判断服务端返回的响应的状态码来确定要采取的操作，使用XMLHttpRequest对象的`status`属性可以获取响应状态码
- 要获取JavaScript解析后的响应体中的内容，我们可以使用XMLHTTPRequest对象的`responseText`属性
