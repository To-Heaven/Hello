# 模板(html + 模板语法)
- 实现前后端的解耦，增加工作效率


#### 注意
- 模板渲染是Django来完成的，Django渲染完成的页面才会发送给客户端浏览器进行渲染
- 不要忘了给form表单的子标签设置name属性
- form表单POST请求提交的数据是每一个子标签对应的value值


## 模板语法
### 模板变量
#### 注意
- 模板变量`{{ var }}`不应该放在`{%  %}`结构内部
- 模板语句的界定符`{% %}`内部不要有圆括号 
- 对象通过句点符只能操作无参数的对象方法。

#### 支持的变量类型
- 数字
- 数组
- 字符串
- 字典
- 对象(类、类的实例)
- 函数

#### 深度查询
> 深度查询即获取容器对象内的元素

- 在模板中可以用句点符`.`访问变量内的存储的值（属性，方法）
	- 对于字典来说`dict.key`
	- 对于列表`list.index`
	- 对于对象(如果直接将对象传入，那么渲染得到的是对象地址，可以使用__str__()方法自定义对象显示格式)
		- `obj.attr`，渲染的是属性值
		- `obj.method`，渲染的是方法的返回值（但是使用句点符不能操作有参函数）


#### 过滤器 filter
- 过滤器对变量进行处理
- 语法
	- `{{ var | filter: parameter }}`

###### 注意
	- **只能传入一个paramater** 
	- 变量调用过滤器的时候，会将变量本身作为第一个位置参数传入到过滤器函数中
		- `filter(var, parameter)`




#### 常用过滤器

- default
	- 当变量是false或者None的时候，为变量设置默认值，否则就是用变量的值
	- `{{ data | default:"无符合条件数据" }}`

- length
	- 返回值的长度。

- filesizeformat
	- 将文件大小字节输出人类可读的类型(KB, MB, bytes)

- date
	- 格式化时间显示
	- `{{ time | date:'Y-m-d' }} `
	- **注意**
		- 没有百分号
		- 引号与冒号之间不要有空格

- slice
	- 对序列进行切片
	- 切片的范围用字符串表示
	- `{{ sequence | slice:"0: -1" }}`

- trancatechars
	- 如果字符串长度大于trancatechars指定的**字符数目**长度，字符串将被截断，剩下没显示的字符串用`...`结尾
	- `{{ content | trancatechars: 20 }}`

- trancatewords
	- 与trancatechars类似，指定的长度为**单词**的长度

- safe
	- 模板渲染时，为了保证安全，渲染引擎会默认将html内容转换成纯文本，比如标签`<script>`会被渲染成`&gt;script&lt;`
	- 如果想让模板渲染时将包含HTML的字符串转换成对应的HTML标签，可以为对象添加safe过滤

```
content = "<a href="">xxxx</a>"


{{ content:safe }}
```

> 简单讲，safe参数可以将你认为安全的JavaScript或者其他能够嵌入到HTML中的内容正常的被解释器编译并执行。对于不被认为安全的内容，将会被转义，转义后的内容自然不会被解释器编译运行。







### 模板语法
- 标签结构
	- `{% grammer %}`

#### 在模板中使用全局上下文变量request
- 客户端发送的请求对象request可以直接在模板中调用，并且不需要通过context上下文中传入到模板。因此我们在模板中就可以直接操作request请求的属性及方法。

- 一个经常使用的地方就是在用户验证的过程中，通过`request.user`可以获取通过auth.authenticate()验证后得到的user对象，并且在模板中，调用request.user我们得到的会时该对象的username(django_user表中的username字段)


#### 循环
- 正向循环
	- 对于列表`{% for ele in container %} ... {% endfor %}`
	- 对于字典`{% for key, value in dict.items %} ... {% endfor %}`

```
{% for ele in container %}
	<p>{{ ele.attr }}</p>
{% endfor %}
```

- 反向循环
	- `{% for ele in container reversed %}`


- forloop变量
	- 循环中使用{{ forloop }}变量来设定随循环变化的序号
	- `forloop.counter` 从1开始计数 
	- `forloop.counter0`从0开始计数
	- `forloop.revcounter`
	- `forloop.revcounter0`
	- `forloop.first` <==> `forloop.count==1`或`forloop.counter0==0` 
	- `forloop.last`

- 如果container为空对象
	- 使用`{% empty %}`分支处理该情况

```
{% for ele in container %}
	执行语句
{% empty %}
	执行语句
{% endfor %}
```


#### 判断
- 结构
	
```
{% if 条件 %}
	<p>xxx</p>
{% elif 条件 %}
	<p>xxx</p>
{% else %}
	<p>xxx</p>
{% endif %}
```


#### with给深度查询中对象取别名
- 方便操作
- 注意
	- 对象取别名时，**等号两边不要有空格，不然会报错**

```
{% with name=obj.attr %}
	执行语句
{% endwith %}
 
```




#### csrf_token
> hidden: 不需要让用户知道，但是又必须要提交的标签内容就使用hidden属性

- 模板引擎对对模板渲染时，`{% csrf_token %}`被解析成一个隐藏的input标签，相当于一个身份证，用户再次发起请求时，如果没有携带该身份证，就会被服务器认为是非法的请求而被forbidden
	- 隐藏的input标签中
		- name属性值为Django中间件，用来验证
		- value属性值为Django为这次请求分配的随机验证码

```html
<form action="/login/" method="post">
	<!--模板渲染之前-->
    {% csrf_token %}
    <p><input type="text" name="user"></p>
    <input type="submit">
</form>

```

```html
<form action="/login/" method="post">
	<!--客户端接收响应中的input-->
    <input type='hidden' name='csrfmiddlewaretoken' value='KOsFQgT1heZAItxgPYAkmOVIIBgOMNeXWXuV6sSaZNN7pMkzrkifYucAi7pBeYJZ' />
    <p><input type="text" name="user"></p>
    <input type="submit">
</form>
```




### 自定义过滤器和标签
#### 自定义过滤器
> 过滤器只能接收两个参数，也就是说除了变量本身之外只能再传入一个变量，自定义标签可以解决参数个数问题

- 步骤
	1. 在项目目录的settings.py文件下配置`INSTALLED_APPS`，将当前app添加到列表中。Django会根据这个配置找到app
	2. 在应用app的文件夹下创建python包`templatetags`。注意，包名称必须是`templatetags`  。
	3. 在包下面创建任意名称的模块，在模块中
		1.  `from django import template`
		2.  `from django.utils.safestring import make_safe`
	4. `register = template.library()`实例化得到library对象，对象名称必须是`register`
	5. 定义过滤器函数，使用装饰器`@register.filter`


#### 自定义标签
> 逻辑判断、循环或其他语法中，不能使用自定义标签，因为大括号会冲突 。可以使用过滤器代替
> 自定义标签调用时参数个数不限制

- 创建自定义标签的过程和自定义过滤器过程基本相同，但是最后的装饰器为`@register.simple_tag`
- 调用
	- `{% simple_tag 参数1 参数2 参数3 %}` 
	- 参数之间用空格隔开



```
# tag.py
from  django import template
from django.utils.safestring import make_safe

register = template.library()		# 名称必须为 register


@register.filter
def multi(var, n):					# var: 使用过滤器的变量， n: 过滤器要传入的参数
	"""对var进行*计算
	
	var为序列： 对多个序列进行拼接
	var为数字： 对数字进行乘法运算
	n为数字，被乘数 ，只能有一个
	"""
	return var * n					


@register.simple_tag
def multi(var, *args):
	"""对var进行*计算
	
	var为序列： 对多个序列进行拼接
	var为数字： 对数字进行乘法运算
	args代表传入的多个数字，被乘数，可以有多个
	""" 
	s = 1
	for i in args:
		s *= i
	return  var * s


@register.simple_tag
def make_input(x, y):
	input_tag = "<input type='text' name='%s' value='%s'>"%(x, y)
	return make_safe(input_tag)
```


#### 使用自定义标签和过滤器  

###### {% load tag.py文件名 %}过程

1. setting.py中installed_app中寻找当前应用
2. 在应用下的templatetags包下寻找导入的`tag.py`文件


```html
{% load tag %}

<p>自定义过滤器{{ a|multi:2 }}</p>

<p>自定义标签{% multi 1 2 3 4 5 6 7 8 9 %}

```


#### 标签和过滤器在模板语法中的使用
1. 与HTML不同，**模板标签内不能包含其他标签，但是可以包含过滤器。**
	- 因此对于想要嵌套的标签，我们可以设计一个形同功能的自定义过滤器来代替
2. 再次声明！模板标签内的模板变量不需要加双大括号`{{  }}`



## 模板继承（extend）
#### block
- {% block name %} 和 {% endblock name %}之间的内容用来被子模版继承覆盖，这块区域当当作一个盒子，子模版导入基模板之后，就可以选择覆盖基模板中的block盒子区域，对于子模版没有覆盖的block盒子，该区域的内容将继承自基模板（即，基模板中block盒子内容总是被用作备选内容）

- 注意
	- `{% endblock name %}`中的name是可选的，但是为了增强可读性和代码的可维护性，应该添加上name

#### 导入
- `{% extends "base.html" %}`
- 注意：
	- **导入模板的语句必须在html文件的最顶行**，否则模板继承将没有用


#### 在blcok中追加内容
- `{% block.upper %}`
- 所谓追加，就是即要保留基模板block中的内容，又要在其基础上追加其他内容。
- 追加内容的位置与`{% block.upper %}`有关





## Template类与Context类
> 这两个方法其实是模块级别render(request, str_template[, context])的后两个参数。在实际使用中，直接使用render函数即可

- Template类实例化可以得到一个模板对象
	- `t = Template(str_page)`

- Context类实例化可以得到一个上下文对象，可以存放模板中变量与值的键值对
	- `c = Context(dict)`

- 创建模板对象html页面
	- `html = t.render(c)`

```python
def timer(request):
    str_time = str(datetime.datetime.now())
    
	# return render(request, 'timer.html', {'timer': str_time[:18]})
   
	from django.template import Template, Context
    
	t = Template('<p>{{ timer }}</p>')
    c = Context({'timer': str_time})
    html = t.render(c)          # 只包含Template中的内容，不会是整个timer.html页面
    return HttpResponse(html)
```

 

## 注意事项
#### 渲染
- Django渲染模板在服务端完成，经过WSGI提供的通信向客户端返回响应字符串


#### script
- 在script中渲染时，要注意上下文传入的字符串在script中会以变量的形式存在于JavaScript语法中，JavaScript解释器将找不到该变量值。

```python
# views.py
def test(request):
    name = 'ziawang'
    age = 18
    return render(request, 'test.html', {'name': name, 'age': age})
```


```html
<h1>{{ name }}</h1>
<h1>{{ age }}</h1>
<script>
        alert('{{ name }}');
{#        alert({{ name }});#}

</script>
```



#### static中的script
- js, css会额外再发送一起请求获取js, css文件，但是不会对该文件尽心个模板渲染，模板内是什么，就会返回什么


```python
# views.py
def test(request):
    name = 'ziawang'
    age = 18
    return render(request, 'test.html', {'name': name, 'age': age}) 
``` 

```javascript
alert('{{ name }}');
```

```html
<h1>{{ name }}</h1>
<h1>{{ age }}</h1>
<script>
        alert('{{ name }}');
{#        alert({{ name }});#}

</script>

<script src="/static/common.js"></script>
```