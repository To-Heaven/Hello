# 模板
- **模板包含了web服务端要发送的相应文件**，Flask使用的是jinja2的模板引擎。
- 使用模板的优点
	- 将业务逻辑和表现逻辑两个过程分隔开，提高程序的可维护性
		- Flask中，所谓业务逻辑就是视图函数与后端数据库之间交互的过程
		- Flask中，表现逻辑就是视图函数通过获取的数据与客户端之间进行交互的过程
- 渲染模板
	- 渲染模板就是利用请求上下文，对模板文件中的变量进行传值和逻辑处理，从而得到做种响应给客户端的字符串的过程

- 位置
	- Flask程序中，模板文件要放在`/templates`目录下

## 规则
- 占位符
	- `{{}}` 内可以是任意python类型的对象
		- 变量
		- 表达式、函数、对象
		- 列表、字典、集合、元组等


- 变量
	- 模板文件中的变量用占位符包含起来`{{ variable }}`
	
- 变量过滤器
	- 待补充

- 条件控制
	-`{% if 条件 %}...{% else %}...{% endif %}`

```
{% if user %}
	hello, {{ name }}
{% else %}
	hello, Stranger
{% endif %}
``` 

- 遍历
	- `{% for ele in eles %}...{% endfor %}`

```
<ul>
	{% for ele in eles %}
		<li>{{ name }}<li>
	{% end for %}
</ul>

```

- 宏（函数）
	- `{% macro func(arg) %} ... {% endmacro %}`

```


```

- 导入`.html`文件
	- 使用`import 'xx.html' [as xxx]`
		- 注意
			- 目标文件要用引号包含起来
			- 应使用as 关键字为文件取别名，方便对文件中的对象进行调用
	- 定义的`宏macro`就可以按照功能分类存放在不同的HTML文件中，再使用import 导入	 



- 导入其他文件中的代码
	- `{% include 'common.html' %}`
	- 将要在多个地方重复使用的代码保存到文件中，在需要导入的地方使用此命令即可


## 模板继承
- 定义`块`区域
	- `{% block area_name %}`
		- `块`区域开始的位置
		- area_name根据实际来命名，比如`{% block head %}, {% blcok title %}, {% block main_content %}`
	- `{% endblock %}`
		- `块`区域结束的位置
	- `{% block area %}`与`{% endblock %}`应该成对出现，一个`块`区域中可以嵌套另一个`块`区域
	- **block标签定义的区域可以在集成之后在在模板中修改**

```html
<!--将标签元素放在块区域之外可以使继承更灵活-->
<head>
{% block head %}
	<title>{% block title %}{% endblock %}</title>
{% endblock %}
</head>
```

- 通过创建一个基模板`base.html`，将继承后，将可能需要子文件自己修改的内容部分或区域使用`块`区域包含起来，对于子模板中不需要修改的内容（比如页眉和页脚等）就不需要包含在`块`区域中

```html
<html>
<head>
 {% block head %}
 <title>{% block title %}hello ziawang{% endblock %} - My Application</title>
 {% endblock %}
</head>
<body>
 {% block body %}
 {% endblock %}
</body>
</html>
```

- 在子模版中继承
	- `{% extends "base.html" %}`
	
- 获取基模板`块`区域中的内容
	- 使用 `{% extends "base.html" %}`之后，子模板继承自基模板中的块就会在子模板中重新定义。如果想获取对应`块`在基模板中的内容，在该块中使用`{{ super() }}`

## 渲染模板
- 导入Flask包中的`render_template`对象
- `render_template('example.html', model_var=arg)`
	- `example.html`是要渲染的模板文件
	- `model_var`是模板文件中的变量
	- `arg`是上下文中的变量（值）/数据

```python


```


