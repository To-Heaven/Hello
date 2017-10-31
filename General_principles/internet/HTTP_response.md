## HTTP响应response
- 响应即服务端收到客户端发送来的请求之后，针对该请求做出的反应，并将结果返回。


#### 响应信息的结构

```
状态行
响应头
响应体

```


## 状态行
- 相应信息的第一行，包含状态码、HTTP协议版本以及状态码对应的原因短语
- 结构
	- `http-version  status-code reason-phrase`

##### status-code
-  常见的status-code状态代码以及对应描述
	-  1xx   信息：客户端发来的reques请求已经接收，需要请求者继续执行操作
	-  2xx  成功：客户端请求已经被成功接收并处理
		-  200   OK   客户端请求成功(GET, POST)
		-  201    Created  已创建，成功请求并创建了新的资源
		-  202    Accepted   已接受。已经接受请求，但未处理完成
	-  3xx   重定向，需要进一步的操作以完成请求
		-  301	页面永久性转移(Permanently Moved)，301重定向是网页更改地址后对搜索引擎最友好的方法，只要不是暂时搬移的情况，都建议使用301来做转址
		-  302	暂时性转移(Temporarily Moved )，在前些年，不少Black Hat SEO曾广泛应用这项技术作弊
		-  305   使用代理。所请求的资源必须通过代理访问
	- 4xx    客户端错误，包括语法错误或无法完成请求
		- 400    客户端请求的语法错误，服务器无法理解
		- 401     请求要求用户的身份认证
		- 403     服务器理解请求客户端的请求，但是拒绝执行此请求
		- 404    请求的资源不存在，无效的URL地址
		- 408    服务器等待客户端发送的请求时间过长，超时
		- 411      服务器无法处理客户端发送的不带Content-Length的请求信息
	- 5xx    服务器错误，服务器在处理请求的过程中发生了错误
		- 500 内部服务器错误

## 响应体
- Allow     请求方法，如Method
- Content-Encoding         文档的编码（Encode）方法。
- Content-Length     内容长度
- Content-Type     表示后面的文档属于什么MIME类型
	- MIME是描述消息内容类型的internet标准
	- MIME可能包含文本、图像、音频、视频等其他应用程序专用的数据
		- `application/msword`  对应扩展名  `doc`
		- `application/oct-stream` 对应扩展名  `bin` 或`exe` 或`class`
		- `application/pdf` 对应扩展名 `pdf`
		- `image` 对应扩展名  `jpg`或`jpeg`
		- `text/html` 对应扩展名 `html`
		- `text/css` 对应扩展名  `css`
		- 等等等，不一一列举
- Date     当前的GMT时间
- Expires     应该在什么时候认为文档已经过期，从而不再缓存它？
- Last-Modified     文档的最后改动时间
- Location     表示客户应当到哪里去提取文档
- Refresh     表示浏览器应该在多少时间之后刷新文档，以秒计
- Server     服务器名字（由web服务器自己设置）
- Set-Cookie     设置和页面关联的Cookie
- WWW-Authenticate     客户应该在Authorization头中提供什么类型的授权信息


## 响应内容
- 和请求内容一样，响应内容是服务端针对客户端请求处理之后返回的主要内容。它要与请求头之前有一个空行隔开


