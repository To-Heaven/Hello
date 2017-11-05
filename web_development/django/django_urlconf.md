# urls.py

```python
# 初始内容
from django.conf.urls import url
from django.comtrib import admin

urlpatterns = {
	url(r'^admin/$', admin.site.urls),
}
```


## urlpatterns 
### 注意
###### url映射匹配是有顺序的
- 一旦成功匹配到第一个，就不再继续
	- 我们可以充分利用这一点，设计一个`404`响应

###### url格式
- regex中的URL前面不需要添加反斜杠
- **使用原始字符串**

###### 验证规则 
- url验证时，不验证请求的方法，不管是GET还是POST或者其他，匹配到同一个URL的请求将路由到同一个视图函数，因此我们需要在视图函数中针对请求方式进行针对性的处理

- url验证在请求的URL上查找，将它当做一个普通的Python 字符串。不包括GET和POST参数以及域名。
	- `http://www.ziawang.com/python/`请求中，url验证将在路由中查找`python/`
	- `http://127.0.0.1:8080/blog/login?username=ziawang&password=pwd`请求中，url验证将在路由中查找`/blog/login`而不会管`?`之后GET请求提交的信息



### url(regex, view, kwargs=None, name=None)
##### 参数regex
- 匹配url地址
- 分组
	- 无名分组
		- 将匹配到的值按位置向视图函数传参
	- 有名分组
		- 将匹配到的值按关键字向视图函数传参

```python
from myapp import views 

urlpatterns = {
	url(r'^2016/09$', views.showtime1),					# 对应 showtime(request)
	url(r'^(\d{4})/(\d{2})$', views.showtime2),			# 对应 showtime(request, year, month)
	url(r'^(?P<year>\d{4})/(?P<month>\d{2})$', views.showtime3)	# 对应 showtime(request, year=xxxx, month=xx)									
}
```

##### 参数views视图函数
- views参数对应views.py中的视图函数名

- 给视图函数设定默认值
	- 可以在匹配失败时调用默认值


```python
# urls.py
urlpatterns = {
	url(r'^2016/09$', views.showtime1),				
	url(r'^2016/(?P<month>\d{2})$', views.showtime3)		
}

# views.py

def showtimes(request, month='10'):
	return HttpResponse('<h1>month</h1>')
```


##### 参数kwargs
- 字典形式
	- 存放要传递给视图函数的额外关键字参数


### URL反向解析
- 在视图函数的重定向和HTML页面中我们经常会传入一些URL路径，对于同一个URL路径，我们可能要在很多地方传入它，那么问题来了，当我们需要修改这个路径的时候，如果采取这种硬编码的形式，那么我们修改这些URL的时候会遇到两种情况
	1. URL过多，每一个地方都要修改，因此我们可能会遗漏某些URL，到值无效的URL地址
	2. 手动修改错误，修改成了一个无效的URL
	3. 还有....

- 解决这种问题最根本的思路就是使用变量来代替变化着的URL，Django给我们提供了这些

##### 1. 配置url()中name参数
- `url(r"path^$?", views.test, name="xxx")`
	- `name`属性对应的是一个字符串，用来代替映射关系中的path
	- path
		- 如果path中没有正则表达式的分组，在HTML中我们可以直接使用`url`关键字来传递路径`{% url "xxx" %}`
		- 如果path中存在分组
			- 无名分组，`url(r"/my/path/(\d+){4}/(\d+){1,2}")`
				- 就要在`"xxx"`后面加上将要传递到path的分组中的值
				- `{% url "xxx" 2016 5 %}`
			- 有名分组，`url(r"/my/path/(?P<year>\d+){4}/(?P<month>\d+){2}")`
				- 就需要使用关键字参数传递变量
				- `{% url "xxx" year=2016 month=5 %}

###### 2.使用django.url.reverse反向生成url
- 问题又来了，如果使用上面的方法，我们只能在模板中来反向生成字符串，如果要在视图函数中呢？Django为我们提供了一个函数`reverse(name, args=(), kwargs=dict())`
- 导入`from django.core.urlresolves import reverse`
- parameters
	- name: 显然就是在`urls.py`中设置的name对应的字符串
	- args: 如果路径中正则匹配是无名分组，就需要使用元组将参数传递进去
	- kwargs: 如果正则陪陪是有名分组，就需要使用字典将参数传递进去

```
# views.py


def test1(request):
	return redirect(reverse("xxx", args=(2016, 5)))

def test2(request):
	return redirect(reverse("xxx", kwargs={"year": 2016, "month": 5}))
```



### url分发
- 一个项目中的所有应用(app)的url映射关系不应该放在项目的同一个urls.py文件中
	- 对于应用共有的url映射关系，可以写在项目文件夹下的urls.py中
		- 比如，登录注册页面
	- 对于项目下每个应用自己独有的映射关系，应该在应用本身文件夹下建立一个.py文件，用来存储自身web程序中需要处理的路由关系，通常也叫urls.py，这样项目urls.py文件中的路由相当于一个中转路由。要实现url映射的中转，需要在项目urls()函数中使用include函数，实现url分发

- 官方原文

```
"""URLconf URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/1.11/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  url(r'^$', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  url(r'^$', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.conf.urls import url, include
    2. Add a URL to urlpatterns:  url(r'^blog/', include('blog.urls'))
``` 

- 实现

```python
from django.conf.urls import url, include
urlpatterns = [
    url(r'^blog/', include("blog.urls")),
]
 
```

- 放在项目同一个urls.py下
	- 一个应用崩溃会影响到其他应用
	- 多个应用的url放在同一个urls下耦合性太高

- 使用url分发
	- 降低耦合性


### 根目录
- 每一个项目都有一个主页。urls.py中url路由配置如下

```
urlpatterns = [
	url(r'^$', views.index)
]
```

- 这样在浏览器地址栏中输入`www.ziawang.com`就可以直接访问主页了

> 浏览器会默认给url添加后缀`/`



## Django的路由系统
