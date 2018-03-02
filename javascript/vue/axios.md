# 异步提交请求

### $axios.request()
- 这个方法用来提交异步请求，类似`$.ajax()`，其参数列表也是使用一个js对象，这个js对象中包含了请求的关键信息。
- js对象中有哪些参数
	- `url`
	- `method`
	- `data`对应一个字典，表示要提交的数据




#### $axios.request().then()
- 这个方法是一个回调函数，用来处理接收到的响应，then方法的参数列表中对应一个回调函数，这个函数的参数就是`response响应`
- response对象内部封装的是响应相关的所有数据，是一个类似python字典的数据结构
	- response.data获取响应提数据
	- response.status获取响应状态码
	- response.headers获取响应头






