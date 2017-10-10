# Flask web程序的基本结构

## 客户都按与web服务端之间通信的实现
### Flask实例对象
- web服务端通常都是浏览器，浏览器向web服务端发送请求request，服务端接收到这个请求之后，返回一个相应response。Flask提供了基本的客户端与服务端交互的功能，每一个项目开始的时候，都需要实例化一个Flask对象（这个对象其实就是一个WSGI程序），服务端接收到客户端的request时，通过WSGI协议将这个请求转交给Flask对象去处理，Flask对象通过request中的url信息找到与之对应的函数（比如视图函数），指向对应的功能。

- Flask对象的实现

```python
from flask import Flask


myapp = Flask(__name__)			# 实例化得到一个简单的Falsk对象

```


- WSGI协议
	- web server gateway interface  Web服务器网管接口，它定义了web服务器与web程序之间的通信规则以及web程序处理request的规则。服务器通过WSGI协议将接收到的request转交给Flask对象去处理。
	- [了解更多WSGI知识](http://wsgi.readthedocs.io/en/latest/index.html)

### 路由
> route装饰器其实就是告诉Flask对象什么样的URL能触发route修饰的函数，它把一个函数绑定到对应的URL地址上

- 路由本质上就是一种映射关系处理过程的抽象，request的url地址通过这个映射关系匹配到对应的request处理函数。每一个myapp(Falsk对象)接收到的request会通过路由来找到对应的函数，这个函数用来处理请求，比如视图函数，用来生成相应（会将返回页面的信息作为返回值返回），客户都按浏览器页面就会显示对应的内容。
- url地址的结构
	- `http://host[:port][abs_path]`
		- host 目标主机的IP地址
		- port 目标主机上服务器所在端口
		- abs_path 请求的资源在该主机上的绝对路径
	- [HTTP协议](http://www.ziawang.com/python/basic_knowledge_of_python/HTTP_protocol.html)

- 路由的实现
	- Falsk框架中，通过装饰器实现路由，其参数用来匹配url地址，装饰器修饰的就是处理url请求的函数
	- `@myapp.route(abs_path)`
		- abs_path是`/`的时候，对应web程序的根地址，通常作为主页面，对应视图函数index
	- abs_path是请求url中对应的资源路径。
	- 动态部分
		- 参数中还可能存在被尖括号包含起来的动态部分,用于匹配url中的变量（比如用户名、密码），动态部分可以被函数调用处理
		- `@myapp.route(/user/<name>)`
		- 尖括号内的值默认字符串类型，也可以通过声明修改，可以使用的类型有三种，`int float path`，其实path本身也是字符串，但是其中的`/`不是作为路径中的分隔符，而是动态部分的一部分
		- 声明变量类型
			- `int:id`
			- `float:length`

```python
from flask import Flask


myapp = Flask(__name__)

@myapp.route('/')
def index():
	return '<h1>欢迎访问我的博客</h1>'

@myapp.route('/<section_name>')
def get_section(section_name):
	if section_name not in ('python', 'flask', 'javascript'):
		abort(404)
	return f'<h1>欢迎进入{section_name}</h1>'
```

### 启动web服务器
- Flask对象的`run(debug=True)`方法可以启动服务端
- **Flask默认端口为5000**

```python
if __name__ == '__main__':
	myapp.run(debug=True)
```

- 当服务器启动之后，会轮询地等待和处理request
- 关于调试模式
	- 顾名思义，调试模式是用来调试地
	- 如果不开启调试模式，每次修改了web程序代码之后都要手动重启服务端，这明显降低了开发效率，开启了调试模式之后，每当我们修改了web程序地代码，web程序会自动的重新启动，如果修改后的程序出现了错误，我们也能迅速定位错误并解决
	- 实现调试模式有两种途径
		- 直接在run方法中传参
		- 使用Flask对象地debug属性修改`myapp.debug = True`

- 关于run方法地host参数
	- 在debug参数为False时，将host参数设置为`0.0.0.0`可以让操作系统监听公网内所有IP发来地请求


## 应用上下文
- 要保证每个request只能在对应地一个线程中是全局变量，而不能是真个程序地全局变量，否则多线程服务器中多个线程处理的就是同一个请求了，