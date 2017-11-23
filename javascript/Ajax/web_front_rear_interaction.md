# 前后端数据交互
- GET请求
	- 地址栏
	- a标签
	- form
- POST
	- form

[](https://www.zhihu.com/question/26532621)
#### 1. HTML赋值
- 通过对HTML标签name和value属性的赋值来实现前后端交互
	- 常见的比如form表单中的input标签，隐藏的input标签也比较常用（用来传递用户没有必要看到但是确实必须要有的内容，比如`csrftoken`）


##### 优点
- 不占用变量，用js可以自由获取


##### 应用
- 一般用于传递简单数据，也适用于将数据与Dom元素绑定关系



#### 2. 通过模板引擎
- 不论是Django还是flask，他们都有自己的模板引擎


#### 3. 通过script填充json
- 将json数据填充到`script`标签中，在前段可以通过Dom树的节点曹祖哦获取json字符串并解析成对象

###### 优点
- 页面加载完成之后就可以获取数据，不会占用全局变量，并且可以传递大量的数据集合


###### 缺点
- 数据量较大的时候会导致页面初次加载变慢，变慢的原因一方面是因为数据量较大，另一方面是因为服务器后台查询数据并将数据在响应中返回需要时间，对于这种青枯球你敢，我呢吧可以使用Ajax按需加载或者加载等待

###### 应用
- 渲染页面。当页面的Dom节点加载完成之后，就需要将json数据渲染到节点中渲染页面



#### 4. Ajax异步请求
- Ajax可以实现异步的向服务端发送请求，我们可以在不刷新页面的情况下更新页面上的局部区域

###### 优点
- 不占用全局变量和Dom节点，**可以自由控制数据的触发事件**

###### 缺点
- 产生额外的HTTP请求
- [Ajax介绍]()




#### 5. WebSocket
- Ajax类似于日常中的短信，当我们需要向服务端发送一个请求的时候，Ajax会帮助我们与服务端建立联系并发送请求，当一次请求响应结束之后Ajax就会断开当前链接。
- 而WebSocket与Ajax最大的区别在于，Ajax每一次请求响应之后就会结束，而WebSocket则是保持客户端与服务端一直联系
- [](https://www.web-tinker.com/article/20372.html)
- [](https://www.ibm.com/developerworks/cn/web/1112_huangxa_websocket/)
- [](https://zh.wikipedia.org/wiki/WebSocket)
