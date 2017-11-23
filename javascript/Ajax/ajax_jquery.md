# 使用jQuery实现Ajax异步请求


## 使用 $.ajax
#### $.ajax(args, [settings])
- 参数
	- args: 以JavaScript对象的形式传递参数，常用的有
		- url: 	请求的路径（这个url可以从浏览器的network中查看）
		- type: 请求方法
		- data: 请求要发送的数据。data数据对应的结构是一个js对象（json对象也可以），Ajax根据请求头中Content-Type指定的类型封装data中的数据
		- success: 请求成功接收到响应后要执行的回调函数。回调函数的参数是服务端返回的响应体中内容
		- error: 服务端发生错误时，要执行的回调函数。
			- 该回调函数有三个参数分别是`"jqXHR", "textStatus", "err"`
		- complete: 
		- statusCode
		- contentType: 发送信息至服务器时内容的编码类型，用来指明当前请求的数据编码格式，服务端会按照请求头中的content-Type来解析请求体中数据。默认application/x-www-form-urlencoded， `?name=ziawang&password=xxx`
			- `application/json`: ，一旦声明contentType为这个值，data对应的数据就必须是json字符串
		- processData: 声明当前data数据是否要进行编码/预处理，默认为True，当设置为false的时候，data中的数据就不会被Ajax按照contentType转换
		- traditional
		- dataType
			- 与其服务端返回的数据类型，客户端会按照dataType指定的值解析响应内容，如果不指定该值，dataType默认就等于服务端返回的响应头中Content-Type指定的值进行转换
			- dataType的值可以是`"html", "xml", "json", "script", "text"`

```javascript

```





#### $.ajax(settings)



#### 使用json传递Boolean类型数据



#### 异步POST请求403拒绝
- 通过Ajax使用POST方式向服务器端发送异步请求的时候，请求只会携带`$.ajax()`中参数`data`内的数据，要想通过跨站请求伪造中间件验证，需要在data中指定`csrfmiddlewaretoken`的值


- **在渲染模板的时候，script中的数据也会被渲染**，因此`csrfmiddlewaretoken`对应的`{{ csrftoken }}`或者`{% csrftoken %}`也会被渲染，会**被替换成服务器保留的随机字符串**。
- 但是要**注意：模板渲染的只能是视图函数要渲染的模板**，因此下面第一种方式不适用于独立的js静态文件

###### 1. $ajax.setup()
- 如果此种方式所在的js代码一静态文件方式存放在服务器中，

```python
$.ajaxSetup({
    data: {csrfmiddlewaretoken: '{{ csrf_token }}' },
});
```

###### 2. 手动获取
- 我们在模板中使用`{% csrftoken %}`，在模板渲染的时候将其渲染成一个隐藏的input标签，所以我们可以使用js或者jQuery来获取标签，自然就能获取其中的值。是用这种方式可以解决独立静态文件无法渲染`{% csrftoken %}`的问题

```html
<input type="hidden" name="csrfmiddlewaretoken" value="2hGFqWuB55YED5afQwsvniiNmMw08NA7vgjEMvzYzgKYDBVfQQmbG5lqeZT9aEw2">
```
	

- 因此我们可以直接获取这个input标签中的value，作为data中`csrfmiddlewaretoken`的值

```javascript
$.ajaxSetup({
    data: {"csrfmiddlewaretoken":$("[name='csrfmiddlewaretoken']").val();
});

```


- 举例

```
# views.py

def cal(request):
    if request.method == 'POST':
        regex = '^\d+(\.\d+)?$'
        nums = [request.POST.get('a'), request.POST.get('b')]

        flag = False
        for i in nums:
            if not re.match(regex, i):
                flag = True

        if flag:
            result = {'result': False}
            return HttpResponse(dumps(result))
        result = sum([float(i) for i in nums])
        result = {'result': result}
        return HttpResponse(dumps(result))

    elif request.method == 'GET':
        return render(request, 'cal.html')
```


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

</head>
<body>
{% csrf_token %}						// 模板渲染时，生成隐藏的input标签
<input type="text" name="a" class="a">
<input type="text" name="b" class="b">
<button>相加</button>
<input type="text" name="c" class="c">
<span id="errmsg"></span>


<script src="/static/ajax.js"></script>	// 导入js
</body>
</html>
```

```javascript
# ajax.js
    function foo() {
        $('#errmsg').text('');
    }

    $('button').click(function () {
        $.ajax({
            url: '/cal/',
            type: 'POST',
            data: ({
                a: $('.a').val(),
                b: $('.b').val(),
                csrfmiddlewaretoken: $('[name="csrfmiddlewaretoken"]').val(),
            }),
            success: function (data) {
                data = JSON.parse(data);
                if (!data['result']){
                    $('#errmsg').text('格式错误');
                    setTimeout(foo, 3000);				// 错误只显示3秒钟
                }else {
                    $('#errmsg').text('');
                    result = data['result'];
                    $('.c').val(result);
                }
            }
        })
    })
```



###### 3. 使用"jquery.cookie.js" 
- 下载`jquery.cookie.js`或者使用cdn，链接如下

```
https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.js
https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.min.js
```

- 步骤
	1. 在script中导入`jquery.cookie.js`
	2. 在Ajax参数中设置

```html
<script src"https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.min.js"></script>

<script>
$.ajax({
	headers: {"X-CSRFToken": $.cookie("csrftoken")}		// 从`jquery.cookie.js`文件中获取"csrftoken"
})

</script>
```

# jQuery序列化表单

####  jQueryObj.serialize()
- 当前端form表单中填充了数据之后，我们可以通过jQuery.serialize()将form表单中的值按照Content-Type中的指定编码类型进行编码，这种方法无法提交文件内容，具体规则见下面。
- 这个方法会将通过jQuery匹配到的表单按照Content-Type指定的类型进行编码序列化成字符串再提交

- 函数返回值是一个String文本字符串，包含了表单中要提交的数据

###### 注意
1. 没有name属性的表单控件不会被提交
2. 带有disabled属性的表单控件不会被提交
3. 只提交有效控件（比如reset、button不会被提交） 


```html
<form id="ser">
    <p>用户名: <input type="text" name="username"></p>
    <p>密码: <input type="password" name="password"></p>
</form>
```


###### 对整个表单序列化

```javascript
var form_ser = $('#ser').serialize();
console.log(form_ser)
```



###### 对指定表单元素进行序列化

```javascript
var form_ser = $('#ser :text').serialize();
console.log(form_ser)
```