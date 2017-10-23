# 核心功能flash
-  flask包中提供了flash函数，用来向客户端响应一个消息（确认、警告或错误等）

## 在主程序文件中
-  `flash(message)`
	-  message是要相应的信息内容
	-  **注意**
		-  flash必须配合模板文件，将信息渲染进模板中，客户端才能看见
	
```python
fron flask import Flask, render_template, flash, redirect, url_for, session


@myapp.route('/', methods=['GET', 'POST'])
def index():
	form = MyForm()
	if form.valiate_on_submit():
		old_name = session.get('name')
		if (form.name.data is not None) 
				and (old_name != form.name.data):
			flash('looks like you have changed youer name')

		session['name'] = form.name.data
		return redirect(url_for('index'))
	return render_template('index.html', 
							form=form, 
							name=session.get('name'))  
```

## 在模板文件中
- 可以在模板文件中，使用`get_flashed_messages()`获取所有flash函数中的message

```html
{% block content %}
<div class="container">
	{% for message in get_flashed_message() %}
	<div class="alert alert-warning">
		<button type="button" class="close" data-dismiss='alert'>&times;</button>
		{{ message }}
	</div>
	{% endfor %}
</div>	
{% endblock %}


```