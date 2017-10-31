## HTTP 请求
- HTTP请求就是由有客户端使用特定的方法对服务端资源发起的请求


#### 请求信息的结构

```
请求首行
请求头
（这里要空出一行）
请求体
```

## 请求首行
- 请求信息的第一行内容，包含了请求方法、目标资源的位置、HTTP协议版本、换行符CRLF
- 结构

```
Method  URL  http-version CRLF
```

#### 请求方法Method
- **GET**： 向服务端特定的资源发出请求，访问已经被URI识别的资源。
	- GET方法的请求体内容为空，请求体的内容附在URL后面
	- `http://www.ziawang.com/python?user=ziawang&password=pass`
		- `?`
			- 在这里起链接作用，将请求信息与URL分隔开。
			- 还有一个作用，可以用来清除缓存。当我们正常输入一个地址，比如`http://www.ziawang.com/index.html`的时候，是默认调用本地缓存的，如果在后添加上问号，再附上内容，就可以让浏览器起认为这是一个新地址，会重新向服务端获取网页数据。如`http://www.ziawang.com/index.html?testnocache`。
		- `&`
			- 作为不同参数的间隔符
		- `#`
	- GET方法不应当被用于产生“副作用”的操作中，例如在web app中。其中一个原因是GET可能会被网络蜘蛛等随意访问。
	- GET请求特点
		- GET请求提交的数据放在URL之后。
		- GET请求提交的数据大小是有限制的，最多1024字节（浏览器限制）
		- GET方法需要用Request.QueryString来获取变量的值，而POST通过Request.Form来获取变量值
		- GET提交数据会带来安全问题
- **POST**： 向服务端提交数据（表单、文件）。
	- POST方法要提交的数据保存在请求体中。
	- **POST请求可能会导致新的资源建立或已有资源的修改**

- **DELETE**： 请求服务器删除DELETE请求中URI标识的资源
- **TRACE**： 回显服务器收到的请求，用来测试或者诊断
- **HEAD**： 向服务器所要与GET请求相一致的响应，但是响应体不会被返回。常用来测试链接的有效性（是否可以访问、是否存在更新内容）。
- **OPTIONS**： 返回服务器针对特定资源支持的HTTP请求方法。迎来测试服务器的功能性
- **PUT**： 向指定资源（URI）上传内容
- **CONNECT**： HTTP1.1中预留给能够将链接修改为管道方式的代理服务器
- PATCH： 将局部修改应用于某一资源，添加与规范RF5789

#### CRLF
- 换行
	- HTTP协议中，请求首行与请求头不能在同一行

## 请求头（请求信息）
> 在HTTP1.1中，所有的请求头，除了host之外都是可选的

##### 缓存相关

- If-Modified-Since：把浏览器端缓存页面的最后修改时间发送到服务器去，服务器会把这个时间与服务器上实际文件的最后修改时间进行对比。如果时间一致，那么返回304，客户端就直接使用本地缓存文件。如果时间不一致，就会返回200和新的文件内容。客户端接到之后，会丢弃旧文件，把新文件缓存起来，并显示在浏览器中。
	- 例如：If-Modified-Since: Thu, 09 Feb 2012 09:07:57 GMT

- If-None-Match：If-None-Match和ETag一起工作，工作原理是在HTTP Response中添加ETag信息。 当用户再次请求该资源时，将在HTTP Request 中加入If-None-Match信息(ETag的值)。如果服务器验证资源的ETag没有改变（该资源没有更新），将返回一个304状态告诉客户端使用本地缓存文件。否则将返回200状态和新的资源和Etag.  使用这样的机制将提高网站的性能。
	- 例如: If-None-Match: "03f2b33c0bfcc1:0"。

- Pragma：指定“no-cache”值表示服务器必须返回一个刷新后的文档，即使它是代理服务器而且已经有了页面的本地拷贝；在HTTP/1.1版本中，它和Cache-Control:no-cache作用一模一样。Pargma只有一个用法。注意: **在HTTP/1.0版本中，只实现了Pragema:no-cache, 没有实现Cache-Control**
	-  例如： Pragma: no-cache

- Cache-Control：指定请求和响应遵循的缓存机制。缓存指令是单向的（响应中出现的缓存指令在请求中未必会出现），且是独立的（在请求消息或响应消息中设置Cache-Control并不会修改另一个消息处理过程中的缓存处理过程）。请求时的缓存指令包括no-cache、no-store、max-age、max-stale、min-fresh、only-if-cached，响应消息中的指令包括public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate、max-age、s-maxage。
	- Cache-Control:Public 可以被任何缓存所缓存
	- Cache-Control:Private 内容只缓存到私有缓存中
	- Cache-Control:no-cache 所有内容都不会被缓存
	- Cache-Control:no-store 用于防止重要的信息被无意的发布。在请求消息中发送将使得请求和响应消息都不使用缓存。
	- Cache-Control:max-age 指示客户机可以接收生存期不大于指定时间（以秒为单位）的响应。
	- Cache-Control:min-fresh 指示客户机可以接收响应时间小于当前时间加上指定时间的响应。
	- Cache-Control:max-stale 指示客户机可以接收超出超时期间的响应消息。如果指定max-stale消息的值，那么客户机可以接收超出超时期指定值之内的响应消息。

##### 浏览器相关

- Accept：浏览器端可以接受的MIME类型。例如：Accept: text/html 代表浏览器可以接受服务器回发的类型为 text/html 也就是我们常说的html文档，如果服务器无法返回text/html类型的数据，服务器应该返回一个406错误(non acceptable)。通配符 * 代表任意类型，代表浏览器可以处理所有类型，(一般浏览器发给服务器都是发这个)。
	- 例如 Accept: */* 

- Accept-Encoding：浏览器申明自己可接收的编码方法，通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate）;Servlet能够向支持gzip的浏览器返回经gzip编码的HTML页面。许多情形下这可以减少5到10倍的下载时间。如果请求消息中没有设置这个域，服务器假定客户端对各种内容编码都可以接受。
	- 例如： Accept-Encoding: gzip, deflate。

- Accept-Language：浏览器申明自己接收的语言。语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等。如果请求消息中没有设置这个报头域，服务器假定客户端对各种语言都可以接受。
	- 例如：Accept-Language: en-us

- Accept-Charset：浏览器可接受的字符集。如果在请求消息中没有设置这个域，缺省表示任何字符集都可以接受。

- User-Agent：告诉HTTP服务器，客户端使用的操作系统和浏览器的名称和版本。
	- 例如： User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; CIBA; .NET CLR 2.0.50727; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729; .NET4.0C; InfoPath.2; .NET4.0E)。

- UA-Pixels，UA-Color，UA-OS，UA-CPU：由某些版本的IE浏览器所发送的非标准的请求头，表示屏幕大小、颜色深度、操作系统和CPU类型。



- Range：可以请求实体的一个或者多个子范围。
	- 表示头500个字节：bytes=0-499
	- 表示第二个500字节：bytes=500-999
	- 表示最后500个字节：bytes=-500
	- 表示500字节以后的范围：bytes=500-
	- 第一个和最后一个字节：bytes=0-0,-1
	- 同时指定几个范围：bytes=500-600,601-999
	- 但是服务器可以忽略此请求头，如果无条件GET包含Range请求头，响应会以状态码206（PartialContent）返回而不是以200（OK）。

##### 请求内容相关

- Content-Type：请求提交内容的类型
	- 例如：Content-Type: application/x-www-form-urlencoded。

- Content-Length：表示请求消息正文的长度。例如：Content-Length: 38。

##### 链接相关

- Referer：包含一个URL，用户从该URL代表的页面出发访问当前请求的页面。提供了Request的上下文信息的服务器，告诉服务器我是从哪个链接过来的，比如从我主页上链接到一个朋友那里，他的服务器就能够从HTTP Referer中统计出每天有多少用户点击我主页上的链接访问他的网站。
	- 例如: `Referer:http://translate.google.cn/?hl=zh-cn&tab=wT`

- Connection：链接方式
	- 1.1默认。 Connection: keep-alive 当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的TCP连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的连接。HTTP 1.1默认进行持久连接。利用持久连接的优点，当页面包含多个元素时（例如Applet，图片），显著地减少下载所需要的时间。要实现这一点，Servlet需要在应答中发送一个Content-Length头，最简单的实现方法是：先把内容写入ByteArrayOutputStream，然后在正式写出内容之前计算它的大小。
	- 1.0默认。 Connection: close 代表一个Request完成后，客户端和服务器之间用于传输HTTP数据的TCP连接会关闭，当客户端再次发送Request，需要重新建立TCP连接。

- Host：（发送请求时，该头域是必需的）主要用于指定被请求资源的Internet主机和端口号，它通常从HTTP URL中提取出来的。HTTP/1.1请求必须包含主机头域，否则系统会以400状态码返回。 	
	- 例如: 我们在浏览器中输入：`http://www.guet.edu.cn/index.html`，浏览器发送的请求消息中，就会包含Host请求头域：`Host：http://www.guet.edu.cn`，此处使用缺省端口号80，若指定了端口号，则变成：Host：指定端口号。

- Authorization：授权信息，通常出现在对服务器发送的WWW-Authenticate头的应答中。主要用于证明客户端有权查看某个资源。当浏览器访问一个页面时，如果收到服务器的响应代码为401（未授权），可以发送一个包含Authorization请求报头域的请求，要求服务器对其进行验证。



- From：请求发送者的email地址，由一些特殊的Web客户程序使用，浏览器不会用到它。


##### cookie

- Cookie：最重要的请求头之一, 将cookie的值发送给HTTP服务器。


### 请求体
- 请求体中存放客户端要发送给服务端的数据
- 请求体要与请求头之间有一空行，用来区分两者