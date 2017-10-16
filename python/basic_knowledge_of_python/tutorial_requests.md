# requests

- [tomorrow](https://github.com/chineking/cola)
- [grequests](https://github.com/kennethreitz/grequests)

## get a response object of a webpage
> query    问号    <==>   question mark    

- requests.get(url, params)
	- this correspond to the HTTP GET request, others see below
	- params: 
		- a dictionary of strings , the key-value pairs correspond to  data would be given as key/value pairs in the URL after a question mark
			- value can be a container
			- if value is None, this key-value would not be added to the URL's query string
		- using by the keyword argument 

```python
>>> import requests
>>> r = requests.get('http://www.ziawang.com')
>>> r.url
'http://www.ziawang.com/'
>>> r.text
'<!DOCTYPE html>\n<html lang="en">\n  <head>\n    <meta charset="UTF-8">\n    <meta name="viewport" content="width=device-width, initial-scale=1">\n\n<!-- Begin Jekyll SEO tag v2.3.0 -->\n<title>Hello</title>\n<meta property="og:title" content="Hello" />\n<meta property="og:locale" content="en_US" />\n<link rel="canonical" href="http://www.ziawang.com/" />\n<meta property="og:url" content="http://www.ziawang.com/" />\n<meta property="og:site_name" content="Hello" />\n<script type="application/ld+json">\n{"name":"Hello","description":null,"author":null,"@type":"WebSite","url":"http://www.ziawang.com/","image":null,"publisher":null,"headline":"Hello","dateModified":null,"datePublished":null,"sameAs":null,"mainEntityOfPage":null,"@context":"http://schema.org"}</script>\n<!-- End Jekyll SEO tag -->\n\n    <link href="/assets/css/style.css?v=b89cc16c0b8fd12658feb35c674960c87410d0cb" rel="stylesheet">\n  </head>\n  <body>\n    <div class="container-lg px-3 my-5 markdown-body">\n      \n        <h1><a href="http://www.ziawang.com/">Hello</a></h1>\n      \n\n      <p><img src="picture/logo.jpg" alt="" /></p>\n\n<h1 id="get-knowledge">Get Knowledge</h1>\n<ul>\n  <li><a href="/python/">Python教程 </a></li>\n</ul>\n\n<h1 id="site-navigation">Site Navigation</h1>\n\n<ul>\n  <li><a href="https://docs.python.org/3/index.html">python documentation ——最全的python知识库</a></li>\n  <li><a href="http://python-web-guide.readthedocs.io/zh/latest/index.html">python web 入坑指南</a></li>\n  <li><a href="https://www.programcreek.com/python/">码河网，various examples of python for you !</a></li>\n</ul>\n\n<hr />\n\n<p><a href="https://github.com/ZiaWang"><img src="picture/githublogo.jpg" alt="" title="我的github" /></a>      <img src="picture\\qqlogo.jpg" alt="" title="1146877568" /></p>\n\n\n      \n    </div>\n    <script src="/assets/javascript/anchor-js/anchor.min.js"></script>\n    <script>anchors.add();</script>\n  </body>\n</html>\n'
>>> params = {'k1':'v1', 'k2':['v2', 'v3']}
>>> r_params = requests.get('http://www.baidu.com', params=params)
>>> r_params.url
'http://www.baidu.com/?k1=v1&k2=v2&k2=v3'
>>> r_params.text
'<!DOCTYPE html>\r\n<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/bdorz/baidu.min.css><title>ç\x99¾åº¦ä¸\x80ä¸\x8bï¼\x8cä½\xa0å°±ç\x9f¥é\x81\x93</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=ç\x99¾åº¦ä¸\x80ä¸\x8b class="bg s_btn"></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>æ\x96°é\x97»</a> <a href=http://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>å\x9c°å\x9b¾</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>è§\x86é¢\x91</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>è´´å\x90§</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>ç\x99»å½\x95</a> </noscript> <script>document.write(\'<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u=\'+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ \'" name="tj_login" class="lb">ç\x99»å½\x95</a>\');</script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">æ\x9b´å¤\x9aäº§å\x93\x81</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>å\x85³äº\x8eç\x99¾åº¦</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>ä½¿ç\x94¨ç\x99¾åº¦å\x89\x8då¿\x85è¯»</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>æ\x84\x8fè§\x81å\x8f\x8dé¦\x88</a>&nbsp;äº¬ICPè¯\x81030173å\x8f·&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>\r\n'
>>> 
```


- requests.post(url, data)
	- POST
- request.put(url, data)
	- PUT
- request.delete(url)
	- DELETE
- request.head(url)
	- HEAD
- request.options(url)
	- OPTIONS
 


## Response Content
> seamlessly    紧密的，没有空格的       



- r.text
	- return the content of the server's response
	- **Requests will automatically decode content from the server**不需要再解码
	- Most unicode charsets are seamlessly decoded.
	- Requests makes educated guesses about the encoding of the response based on the HTTP headers
		- requests模块在你得到服务器端response之后，就会自动的从HTTP响应(response)报头中获取Content-Encoding
> 更多关于HTTP的内容请访问[HTTP协议](http://www.ziawang.com/python/basic_knowledge_of_python/HTTP_protocol.html)


- r.encoding
	- return the coding of the response's content(the encoding Requests is using)
	- change the encoding using '='
		- ___If you change the encoding, Requests will use the new value of r.encoding_
		- 修改之后，再次调用r.text，错误的编码格式会乱码

```python
>>> r.encoding
'utf-8'
>>> r.encoding = 'gbk'
>>> r.encoding
'gbk'
>>> r.text
'<!DOCTYPE html>\n<html lang="en">\n  <head>\n    <meta charset="UTF-8">\n    <meta name="viewport" content="width=device-width, initial-scale=1">\n\n<!-- Begin Jekyll SEO tag v2.3.0 -->\n<title>Hello</title>\n<meta property="og:title" content="Hello" />\n<meta property="og:locale" content="en_US" />\n<link rel="canonical" href="http://www.ziawang.com/" />\n<meta property="og:url" content="http://www.ziawang.com/" />\n<meta property="og:site_name" content="Hello" />\n<script type="application/ld+json">\n{"name":"Hello","description":null,"author":null,"@type":"WebSite","url":"http://www.ziawang.com/","image":null,"publisher":null,"headline":"Hello","dateModified":null,"datePublished":null,"sameAs":null,"mainEntityOfPage":null,"@context":"http://schema.org"}</script>\n<!-- End Jekyll SEO tag -->\n\n    <link href="/assets/css/style.css?v=b89cc16c0b8fd12658feb35c674960c87410d0cb" rel="stylesheet">\n  </head>\n  <body>\n    <div class="container-lg px-3 my-5 markdown-body">\n      \n        <h1><a href="http://www.ziawang.com/">Hello</a></h1>\n      \n\n      <p><img src="picture/logo.jpg" alt="" /></p>\n\n<h1 id="get-knowledge">Get Knowledge</h1>\n<ul>\n  <li><a href="/python/">Python鏁欑▼ </a></li>\n</ul>\n\n<h1 id="site-navigation">Site Navigation</h1>\n\n<ul>\n  <li><a href="https://docs.python.org/3/index.html">python documentation 鈥斺�旀渶鍏ㄧ殑python鐭ヨ瘑搴�</a></li>\n  <li><a href="http://python-web-guide.readthedocs.io/zh/latest/index.html">python web 鍏ュ潙鎸囧崡</a></li>\n  <li><a href="https://www.programcreek.com/python/">鐮佹渤缃戯紝various examples of python for you !</a></li>\n</ul>\n\n<hr />\n\n<p><a href="https://github.com/ZiaWang"><img src="picture/githublogo.jpg" alt="" title="鎴戠殑github" /></a>      <img src="picture\\qqlogo.jpg" alt="" title="1146877568" /></p>\n\n\n      \n    </div>\n    <script src="/assets/javascript/anchor-js/anchor.min.js"></script>\n    <script>anchors.add();</script>\n  </body>\n</html>\n'
>>> 

```

- r.content
	- return the content by the form of binary 

```python
>>> r.encding = 'utf-8'
>>> r.content
b'<!DOCTYPE html>\n<html lang="en">\n  <head>\n    <meta charset="UTF-8">\n    <meta name="viewport" content="width=device-width, initial-scale=1">\n\n<!-- Begin Jekyll SEO tag v2.3.0 -->\n<title>Hello</title>\n<meta property="og:title" content="Hello" />\n<meta property="og:locale" content="en_US" />\n<link rel="canonical" href="http://www.ziawang.com/" />\n<meta property="og:url" content="http://www.ziawang.com/" />\n<meta property="og:site_name" content="Hello" />\n<script type="application/ld+json">\n{"name":"Hello","description":null,"author":null,"@type":"WebSite","url":"http://www.ziawang.com/","image":null,"publisher":null,"headline":"Hello","dateModified":null,"datePublished":null,"sameAs":null,"mainEntityOfPage":null,"@context":"http://schema.org"}</script>\n<!-- End Jekyll SEO tag -->\n\n    <link href="/assets/css/style.css?v=b89cc16c0b8fd12658feb35c674960c87410d0cb" rel="stylesheet">\n  </head>\n  <body>\n    <div class="container-lg px-3 my-5 markdown-body">\n      \n        <h1><a href="http://www.ziawang.com/">Hello</a></h1>\n      \n\n      <p><img src="picture/logo.jpg" alt="" /></p>\n\n<h1 id="get-knowledge">Get Knowledge</h1>\n<ul>\n  <li><a href="/python/">Python\xe6\x95\x99\xe7\xa8\x8b </a></li>\n</ul>\n\n<h1 id="site-navigation">Site Navigation</h1>\n\n<ul>\n  <li><a href="https://docs.python.org/3/index.html">python documentation \xe2\x80\x94\xe2\x80\x94\xe6\x9c\x80\xe5\x85\xa8\xe7\x9a\x84python\xe7\x9f\xa5\xe8\xaf\x86\xe5\xba\x93</a></li>\n  <li><a href="http://python-web-guide.readthedocs.io/zh/latest/index.html">python web \xe5\x85\xa5\xe5\x9d\x91\xe6\x8c\x87\xe5\x8d\x97</a></li>\n  <li><a href="https://www.programcreek.com/python/">\xe7\xa0\x81\xe6\xb2\xb3\xe7\xbd\x91\xef\xbc\x8cvarious examples of python for you !</a></li>\n</ul>\n\n<hr />\n\n<p><a href="https://github.com/ZiaWang"><img src="picture/githublogo.jpg" alt="" title="\xe6\x88\x91\xe7\x9a\x84github" /></a>      <img src="picture\\qqlogo.jpg" alt="" title="1146877568" /></p>\n\n\n      \n    </div>\n    <script src="/assets/javascript/anchor-js/anchor.min.js"></script>\n    <script>anchors.add();</script>\n  </body>\n</html>\n'
>>> 
```


- r.status_code
	- Integer Code of responded HTTP Status，返回服务端响应的状态码
	- 200   链接成功
	- 404    请求资源不存在，因为是无效的URL地址
- **requests.codes.ok**  
	- a built-in status code lookup object for easy reference


```python
>>> import requests
>>> r = requests.get('http://www.ziawang.com')
>>> r.status_code
200
>>> r.status_code == requests.codes.ok
True
>>> 

```

> raw   原始   
> 原始套接字SOCK_RAW: 一种不同于SOCK_STREAM、SOCK_DGRAM的套接字，它实现于系统核心，普通的套接字无法处理ICMP、IGMP等网络报文，而SOCK_RAW可以；其次，SOCK_RAW也可以处理特殊的IPv4报文；此外，利用原始套接字，可以通过IP_HDRINCL套接字选项由用户构造IP头。总体来说，SOCK_RAW可以处理普通的网络报文之外，还可以处理一些特殊协议报文以及操作IP层及其以上的数据。

- r.raw（非常少用）
	- return the raw socket response from the server 
	- **NOTE**
		- If you want to do this, make sure you set stream=True in your initial request

```python
>>> import requests
>>> r = requests.get('http://www.ziawang.com', stream=True)
>>> r.raw
<urllib3.response.HTTPResponse object at 0x000001815905E080>
>>> r.encoding
'utf-8'
>>> r.status_code
200
>>> r.text
'<!DOCTYPE html>\n<html lang="en">\n  <head>\n    <meta charset="UTF-8">\n    <meta name="viewport" content="width=device-width, initial-scale=1">\n\n<!-- Begin Jekyll SEO tag v2.3.0 -->\n<title>Hello</title>\n<meta property="og:title" content="Hello" />\n<meta property="og:locale" content="en_US" />\n<link rel="canonical" href="http://www.ziawang.com/" />\n<meta property="og:url" content="http://www.ziawang.com/" />\n<meta property="og:site_name" content="Hello" />\n<script type="application/ld+json">\n{"name":"Hello","description":null,"author":null,"@type":"WebSite","url":"http://www.ziawang.com/","image":null,"publisher":null,"headline":"Hello","dateModified":null,"datePublished":null,"sameAs":null,"mainEntityOfPage":null,"@context":"http://schema.org"}</script>\n<!-- End Jekyll SEO tag -->\n\n    <link href="/assets/css/style.css?v=b89cc16c0b8fd12658feb35c674960c87410d0cb" rel="stylesheet">\n  </head>\n  <body>\n    <div class="container-lg px-3 my-5 markdown-body">\n      \n        <h1><a href="http://www.ziawang.com/">Hello</a></h1>\n      \n\n      <p><img src="picture/logo.jpg" alt="" /></p>\n\n<h1 id="get-knowledge">Get Knowledge</h1>\n<ul>\n  <li><a href="/python/">Python教程 </a></li>\n</ul>\n\n<h1 id="site-navigation">Site Navigation</h1>\n\n<ul>\n  <li><a href="https://docs.python.org/3/index.html">python documentation ——最全的python知识库</a></li>\n  <li><a href="http://python-web-guide.readthedocs.io/zh/latest/index.html">python web 入坑指南</a></li>\n  <li><a href="https://www.programcreek.com/python/">码河网，various examples of python for you !</a></li>\n</ul>\n\n<hr />\n\n<p><a href="https://github.com/ZiaWang"><img src="picture/githublogo.jpg" alt="" title="我的github" /></a>      <img src="picture\\qqlogo.jpg" alt="" title="1146877568" /></p>\n\n\n      \n    </div>\n    <script src="/assets/javascript/anchor-js/anchor.min.js"></script>\n    <script>anchors.add();</script>\n  </body>\n</html>\n'
>>> 

```

- r.headers
	- re turn the dictionary of HTTP headers 

```python
>>> import requests
>>> r = requests.get('http://www.ziawang.com')
>>> r.status_code
200
>>> r.status_code == requests.codes.ok
True
>>> head = r.headers
>>> head.get('Content-Encoding')
'gzip'
>>> head['Content-Type']
'text/html; charset=utf-8'
>>> head
{'Date': 'Sun, 03 Sep 2017 10:06:49 GMT', 'Content-Type': 'text/html; charset=utf-8', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'Set-Cookie': '__cfduid=d3738b2e79feeea7a8000fbeb463fe6e51504433209; expires=Mon, 03-Sep-18 10:06:49 GMT; path=/; domain=.ziawang.com; HttpOnly', 'Last-Modified': 'Sat, 02 Sep 2017 10:00:55 GMT', 'Access-Control-Allow-Origin': '*', 'Expires': 'Sun, 03 Sep 2017 07:11:06 GMT', 'Cache-Control': 'max-age=600', 'X-GitHub-Request-Id': 'DDDC:13A45:BCA212:C9092D:59ABA8B2', 'Via': '1.1 varnish', 'Age': '341', 'X-Served-By': 'cache-lax8623-LAX', 'X-Cache': 'HIT', 'X-Cache-Hits': '1', 'X-Timer': 'S1504433210.506233,VS0,VE1', 'Vary': 'Accept-Encoding', 'X-Fastly-Request-ID': '66880f0a24bf1570d6cb73282f1267a1044e931e', 'Server': 'cloudflare-nginx', 'CF-RAY': '3987e60764087924-LAX', 'Content-Encoding': 'gzip'}
>>> 
```


## Methods of response object

- r.json()
	- return the content by the form of json if the content is jsonable object
	- raise an exception while JSON decoding fails
		1. response gets a 204
		2. respon contains invaild JSON
	- **the success of the call to r.json() does not indicate the success of the response**
		- Some servers may return a JSON object in a failed response , example:  505 , and such JSON will be decoded and returned
		-  __To check that a request is successful, use r.raise_for_status() or check r.status_code is what you expect__


- r.iter_content(chunk_size=1, decode_unicode=False)
	- Iterates over the response data
	- **when stream=True is set on the request, this avoids reading the content at once into memory for large responses**
	- *parameters*
		- chunk_size:  the number of bytes it should read into memory
			- must be of type int or None
			- if None:
				- if request stream=True will read data as it arrives in whatever size the chunks are received.将content整个返回，不管多大
				- if request stream=False data is returned as a single chunk.
		- decode_unicode: If decode_unicode is True, content will be decoded using the best available encoding based on the response
			- __decode_unicode  是true的时候，返回的是字符串，此时打开文件的方式记得改成文本模式，而非二进制模式，如果是False，写入文件要使用二进制模式__


```python
import requests

r = requests.get('http://www.ziawang.com', stream=True)

with open('ziawang', 'wb') as wf:			# 二进制模式
    for chunk in r.iter_content(chunk_size=1):
        wf.write(chunk)

```


```python
import requests

r = requests.get('http://www.ziawang.com', stream=True)

with open('ziawang', 'w') as wf:		# 文本格式
    for chunk in r.iter_content(chunk_size=1, decode_unicode=True):
        wf.write(chunk)
```


- r.raise_for_status()
	- Raises stored HTTPError, if one occurred
	- if `status_status == requests.codes.ok` , return None


```python
>>> r = requests.get('http://www.ziawang.com/status/404')
>>> r.status_code
404
>>> r.raise_for_status()
Traceback (most recent call last):
  File "<pyshell#21>", line 1, in <module>
    r.raise_for_status()
  File "D:\Python36\lib\site-packages\requests\models.py", line 935, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 404 Client Error: Not Found for url: http://www.ziawang.com/status/404
>>> 
```


## Errors while Connectting failed

```python
>>> r = requests.get('http://www.xvideos.com')
Traceback (most recent call last):
  File "D:\Python36\lib\site-packages\urllib3\connectionpool.py", line 601, in urlopen
    chunked=chunked)
  File "D:\Python36\lib\site-packages\urllib3\connectionpool.py", line 387, in _make_request
    six.raise_from(e, None)
  File "<string>", line 2, in raise_from
  File "D:\Python36\lib\site-packages\urllib3\connectionpool.py", line 383, in _make_request
    httplib_response = conn.getresponse()
  File "D:\Python36\lib\http\client.py", line 1331, in getresponse
    response.begin()
  File "D:\Python36\lib\http\client.py", line 297, in begin
    version, status, reason = self._read_status()
  File "D:\Python36\lib\http\client.py", line 258, in _read_status
    line = str(self.fp.readline(_MAXLINE + 1), "iso-8859-1")
  File "D:\Python36\lib\socket.py", line 586, in readinto
    return self._sock.recv_into(b)
ConnectionResetError: [WinError 10054] 远程主机强迫关闭了一个现有的连接。

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "D:\Python36\lib\site-packages\requests\adapters.py", line 440, in send
    timeout=timeout
  File "D:\Python36\lib\site-packages\urllib3\connectionpool.py", line 639, in urlopen
    _stacktrace=sys.exc_info()[2])
  File "D:\Python36\lib\site-packages\urllib3\util\retry.py", line 357, in increment
    raise six.reraise(type(error), error, _stacktrace)
  File "D:\Python36\lib\site-packages\urllib3\packages\six.py", line 685, in reraise
    raise value.with_traceback(tb)
  File "D:\Python36\lib\site-packages\urllib3\connectionpool.py", line 601, in urlopen
    chunked=chunked)
  File "D:\Python36\lib\site-packages\urllib3\connectionpool.py", line 387, in _make_request
    six.raise_from(e, None)
  File "<string>", line 2, in raise_from
  File "D:\Python36\lib\site-packages\urllib3\connectionpool.py", line 383, in _make_request
    httplib_response = conn.getresponse()
  File "D:\Python36\lib\http\client.py", line 1331, in getresponse
    response.begin()
  File "D:\Python36\lib\http\client.py", line 297, in begin
    version, status, reason = self._read_status()
  File "D:\Python36\lib\http\client.py", line 258, in _read_status
    line = str(self.fp.readline(_MAXLINE + 1), "iso-8859-1")
  File "D:\Python36\lib\socket.py", line 586, in readinto
    return self._sock.recv_into(b)
urllib3.exceptions.ProtocolError: ('Connection aborted.', ConnectionResetError(10054, '远程主机强迫关闭了一个现有的连接。', None, 10054, None))

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<pyshell#10>", line 1, in <module>
    r = requests.get('http://www.xvideos.com')
  File "D:\Python36\lib\site-packages\requests\api.py", line 72, in get
    return request('get', url, params=params, **kwargs)
  File "D:\Python36\lib\site-packages\requests\api.py", line 58, in request
    return session.request(method=method, url=url, **kwargs)
  File "D:\Python36\lib\site-packages\requests\sessions.py", line 508, in request
    resp = self.send(prep, **send_kwargs)
  File "D:\Python36\lib\site-packages\requests\sessions.py", line 618, in send
    r = adapter.send(request, **kwargs)
  File "D:\Python36\lib\site-packages\requests\adapters.py", line 490, in send
    raise ConnectionError(err, request=request)
requests.exceptions.ConnectionError: ('Connection aborted.', ConnectionResetError(10054, '远程主机强迫关闭了一个现有的连接。', None, 10054, None))
>>> 

```