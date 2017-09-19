# HTTP协议
## 介绍
- TCP/UDP协议、HTTP协议、socket套接字
	1. 在TCP五层模型中，HTTP位于最上层的应用层，属于应用层的面向对象的协议
		- 为什么说http是面向对象的协议呢?
			-  首先要明确的是，这里的面向对象和程序设计里面的面向对象的概念是不一样的。
			-  http协议所说的面向对象，是指HTTP协议是允许传输任意类型的数据对象，传输对象的数据类型是由content-Type来标记的
	2. HTTP协议基于TCP/UDP通信协议来传递数据，如HTML文件，图片，视频等
		- 每一次客户端发送的request请求都会从服务端返回一个response响应，并且在每一次请求结束之后都会主动释放链接
			-  在1.0版本的HTTP协议中，客户都按每一次request和服务端返回的response都会建立一个单独的链接，在本次链接任务完成之后，都会释放该链接
			-  在1.1版本的HTTP协议中，服务端可以并行同时处理从客户端接收到多个request，不再像1.0版本中串行的方式，大大提高了传输效率
	3. TCP协议是传输层的协议，他建立了端口到端口之间的通信
		- 三次握手和四次挥手请看网络通讯原理介绍
	4. socket套接字是TCP/UDP协议进行网络通信的基本操作单元，本身不是协议，而是一个调用接口API 
		- socket套接字中包含了网络通信必须的五种信息
			1. 协议：udp、tcp等（传输层）
			2. 本地主机ip（网络层）
			3. 本地进程端口（传输层）
			4. 目标主机ip（网络层）
			5. 目标主机端口（传输层）
		-  从编写程序的角度来看，socket套接字是对TCP/UDP协议的封装和应用，通过socket套接字我们才能使用TCP/UDP协议


```html
# 一个简单的http报头
Date: Sun, 10 Sep 2017 08:49:16 GMT 
Content-Type: text/html; charset=utf-8 
Transfer-Encoding: chunked 
Connection: keep-alive 
Set-Cookie: __cfduid=d58e12f330997afce88fd460f40f8e1d21505033356; expires=Mon, 10-Sep-18 08:49:16 GMT; path=/; domain=.ziawang.com; HttpOnly 
Last-Modified: Sun, 10 Sep 2017 02:17:08 GMT 
Access-Control-Allow-Origin: * 
Expires: Sun, 10 Sep 2017 08:12:59 GMT 
Cache-Control: max-age=600 
X-GitHub-Request-Id: 3B3C:7058:F5339D:164566A:59B4F1B2 
Via: 1.1 varnish 
Age: 164 
X-Served-By: cache-hkg17929-HKG 
X-Cache: HIT 
X-Cache-Hits: 1 
X-Timer: S1505033356.393947,VS0,VE1 
Vary: Accept-Encoding 
X-Fastly-Request-ID: 99885d489440fe08625745541a035dbcd481fc4b 
Server: cloudflare-nginx 
CF-RAY: 39c1220d4239882d-HKG 
Content-Encoding: gzip 

```



> TCP/UDP协议解决的是数据在网络中的传输，而HTTP解决的是如何包装数据



- HTTP协议特点
	1. 简单快速，客户端只需要提供请求方法和资源路径，而且HTTP协议简单，使得服务端的程序规模小，所以通讯比较快
	2. 无连接，上面说到，HTTP的客户端发送request给服务端，服务端返回一个对应的response之后就会释放建立的链接。即无连接指每次客户端和服务器建立的连接只能完成一套request和response操作
	3. 无状态，保存状态即是对任务或事件的进度的保存，以便下次从保存位置继续开始该任务或事件。HTTP无状态就是指HTTP对事物处理的整个过程都不会对该事件状态进行保存的，因此如果HTTP协议后续处理需要前面任务或事件的信息，就必须重传，这回导致每次链接传送的数据量变得更大，但是这样也有优点，如果服务器后续处理事件并不需要先前事件的信息，那么服务器的应答效率会提高
	4. HTTP服务器允许传输任意类型的数据对象。

## HTTP协议url结构
- `http://host[:port][abs_path]`
	- host：目标主机的ip地址
	- port：目标主机的端口号，默认是80，可以修改
	- abs_path：请求的资源在该主机上的绝对路径
- **Note**
	- port  和  abs_path是可选项
	- 如果在输入目标主机IP的时候，没有输入资源路径，而是直接访问主机IP，web浏览器会自动的为我们在port后面加上反斜杠'/'
	- `http://www.baidu.com`    <==> `http://www.baidu.com:80/`

## HTTP请求request
- 结构 
	- `Method  URL HTTP-Version CRLF `
- Method请求方法
	- GET：获取URL地址标识页面的资源
	- POST：向指定URL提交数据，数据被包含在请求体中。
		- 会导致新资源的建立和旧资源的修改
	- HEAD：获取URL地址标识页面的报头
		- 用于测试链接的有效性即链接是否能够被访问，而不必获取全部页面资源
		- 也可以用于检查页面是否更新
	- PUT： 请求URL指向的服务器存储由客户端发送给服务端的资源
	- DELETE：请求服务端主机删除URL所指定页面
	- CONNECT
	- OPTIONS：通过客户端查看服务器的性能
	- TRACE：回显服务器收到的请求，主要用于测试或诊断。
- URL(uniform resource locator) 统一资源定位符， 互联网上的每一个文件都有一个唯一的URL地址
- CRLF是换行符 


- URL与URI
	- mark待补充

## HTTP响应response
- 响应结构
	- `状态行  消息报头  响应正文`
- 状态行结构
	- `HTTP-Version  Status-Code  Reason-Phrese CRLF`
	-  常见的status-code状态代码以及对应描述
		-  1xx   信息：客户端发来的reques请求已经接收，需要请求者继续执行操作
		-  2xx  成功：客户端请求已经被成功接收并处理
			-  200   OK   客户端请求成功(GET, POST)
			-  201    Created  已创建，成功请求并创建了新的资源
			-  202    Accepted   已接受。已经接受请求，但未处理完成
		-  3xx   重定向，需要进一步的操作以完成请求
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
		
- 报头内容
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


## SYN洪水攻击
> SYN  ---> synchronize  同步    
> ACK  ---> acknowledgement    确认   


- 首先，HTTP是基于传输层TCP/UDP协议来传输协议的，TCP协议建立链接需要三次握手，断开连接需要四次挥手
	- 第一次：请求端（客户端）发送一个包含SYN标志的TCP报文，同步报文会指明客户端使用的端口以及TCP连接的初始序号
	- 第二次：服务器在收到客户端的SYN报文后，将返回一个SYN+ACK的报文，表示客户端的请求被接受，同时TCP序号被加1
	- 第三次：客户端也返回一个确认报文ACK给服务器端，同样TCP序列号被加一，到此一个TCP连接完成。

- 原理：请求端发送了一个SYN报文之后就掉线，服务器返回给请求端的SYN+ACK之后会等待客户端返回一个ACK报文确认，如果此时请求端掉线或死机，那么服务端就无法接受到这个ACK报文，即第三次握手无法完成，于是服务器在一段时间之内会重新尝试向请求端发送SYN+ACK报文，超出这段时间之后才会丢弃该未完成的链接，这个等待时间就是SYN Timeout（30~120秒）。
- SYN洪水攻击
	- 即服务器端忙于处理大量攻击者伪造的TCP请求而无暇顾及正常客户请求，使得客户端向服务端发送的request请求无法获取到response响应，即服务器失去正常响应能力
- 防御
	- 缩短SYN Timeout时间，间接降低攻击者伪造的半链接数目
		- 仅仅在攻击者攻击频率低的情况下有效
	- 设置SYN Cookie，给每一个请求链接的IP地址分配一个Cookie，如果短时间内连续受到某个IP的重复SYN报文，就认定是受到了攻击，以后从这个IP地址来的包会被一概丢弃。
		- SYN Cookie 依赖攻击者使用真实的IP地址
