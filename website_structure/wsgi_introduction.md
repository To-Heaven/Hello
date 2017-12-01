## WSGI协议
- 在了解WSGI协议之前我们有必要区分一下Web服务器与Web应用程序


#### Web服务器
- Web服务器用来处理客户端请求，并且将请求转发给Web应用，严格上来讲，Web服务器只能用来处理HTTP请求和响应信息，并且只能向用户发送静态页面的内容，对于动态页面的内容，幼通过CGI、FastCGI等接口交给Web程序去处理
- 常见的Web服务器报货Nginx、Apache等

#### Web应用程序
- Web应用程序一般也支持HTTP协议，用来处理业务逻辑，比如我们的python开发中常用的Django、Flask框架

#### 为什么要有WSGI协议
- 对于每一个Web应用程序来说，都需要有一个Web服务器与其进行通信，如果Web应用程序与Web服务器之间的协议（你可以理解为沟通的语言）是不同的，那么Web服务器与Web应用程序之间就无法沟通，导致无法处理客户端的请求。一次你，就像HTTP协议一样，python定义了一个python的Web服务器与Web框架之间沟通的通用协议，这就是WSGI。有了WSGI，Web框架就可以与任意一个实现了WSGI协议的Web服务器进行联系并处理客户端请求了。

#### 使用uwsgi替代Django、Flask自带服务器
- 为了开发方便，Django和Flask都内置了Web服务器，在正式生产环境中，我们要用其他Web服务器替换内置的服务器。
 