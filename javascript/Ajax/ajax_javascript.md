# 使用JavaScript实现Ajax异步请求

## 概要
- 在面向对象设计中，要想实现或者调用一个对象的功能，首先我们要通过实例化的到这个对象。在JavaScript中，要想实现Ajax异步请求，我们需要实例化的到一个`XMLHttpRequest`对象，通过调用该对象的方法，我们可以实现下面几件事儿
	1. 创建链接
	2. 发送请求
	3. 接收响应
	4. 读取服务端响应内容


## 实现Ajax异步请求
#### 创建XMLHttpRequest对象
- 创建一个对象很简单，代码如下

```javascript

xmlHttp = XMLHttpRequest();

```



###### 兼容性
- 在不同的浏览器中(IE)，实例化得到XMLHttpRequest对象的方法也是不同的，因此在开发中，我们要考虑到浏览器的兼容。
- 因此我们将实例化对象的过程封装到函数中，代码如下

```javascript	
function createXMLHttpRequest() {
        var xmlHttp;
        // 适用于大多数浏览器，以及IE7和IE更高版本
        try{
            xmlHttp = new XMLHttpRequest();
        } catch (e) {
            // 适用于IE6
            try {
                xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
            } catch (e) {
                // 适用于IE5.5，以及IE更早版本
                try{
                    xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
                } catch (e){}
            }
        }            
        return xmlHttp;
    }


var xmlHttp = createXMLHttpRequest();
```

#### 使用open建立链接
- `xmlHttp.open(method, url, async=true)`
	- method: 请求方法
	- url: 请求路径
		- 对于`GET`请求，我们需要在url后面附上请求参数!
		- `'/home/?user=ziawang&age=18'`
	- async: 是否异步发送请求


#### 使用send发送请求
- `xmlHttp.send(request_body || null)`
	- 参数代表请求体中的内容，请求为GET请求的时候，我们必须要为该方法传入一个null
	- `xmlHttp.send('name=ziawang&age=18')`



#### 接收服务器响应
###### XMLHttpRequest对象的五个状态
- 在整个异步请求的生命周期中，请求对象有五个状态，每一个状态都对应一个**整数**
	- `0`: 尚未与服务端使用`open`建立连接
	- `1`: 已经以服务端`open`建立链接，但是没有使用`send`发送请求
	- `2`: 已经发送请求，但是处于等待接收服务端响应结果的状态
	- `3`: 接收到服务端的响应结果，并使用JavaScript进行解析
	- `4`: 解析结束，断开请求

###### 接收响应
- 使用对象的`onreadystatechange`触发事件，每一次请求状态的改变，都会触发一次该事件（除了0代表的状态）
- 因此我们可以通过判断请求的状态值来指定请求处于每一个状态对应的事件。
- XMLHttpRequest对象的`readyState`属性可以获取对象当下所处的状态
- 有时我们还要通过判断服务端返回的响应的状态码来确定要采取的操作，使用XMLHttpRequest对象的`status`属性可以获取响应状态码
- 要获取JavaScript解析后的响应体中的内容，我们可以使用XMLHTTPRequest对象的`responseText`属性


```javascript
function createXMLHttpRequest() {
        var xmlHttp;
        // 适用于大多数浏览器，以及IE7和IE更高版本
        try{
            xmlHttp = new XMLHttpRequest();
        } catch (e) {
            // 适用于IE6
            try {
                xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
            } catch (e) {
                // 适用于IE5.5，以及IE更早版本
                try{
                    xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
                } catch (e){}
            }
        }            
        return xmlHttp;
    }
	

var xmlHttp = createXMLHttpRequest();
var btn = document.getElementByTagName("button");

btn.onclick = function(){
	xmlHttp.open('get', '/home/?name=ziawang&age=18');
	xmlHttp.send(null);
	
	xmlHttp.onReadyStateChange = function(){
		if (this.readState == 4 && this.status == 200){
			consol.log(this.responseText)
		}else if(this.status == 4 && this.status == 500){
			alert("服务端出错啦")
		}
	}
}

```

##### 原生js的Ajax注意事项
0. 原生的js发送的Ajax请求，如果Django服务端使用的是`request.isAjax()`来处理对应的请求，需要我们在Ajax发送请求的时候在请求头中添加一组键值对，但是如果我们使用的是jQuery封装的Ajax请求，不需要添加键值对，在后端也可以使用`request.isAjax()`来判断并处理请求。
	- `xmlHttp.setRequestHeader("X-Requested-With",'XMLHttpRequest');`。
	- 注意，如果请求是POST提交的，我们直接使用`request.method == 'POST'`判断处理Ajax请求也可以
	- 为什么要在请求投中添加上这一组键值对呢？看下面源码你就知道了
		- 下面的源码中，判断是否是ajax请求的条件就是能否在请求头(request.META)中get获取到`HTTP_X_REQUESTED_WITH`对应的`XMLHttpRequest`值，所以我们需要在原生Ajax请求中添加这一组键值对

```python
    def is_ajax(self):
        return self.META.get('HTTP_X_REQUESTED_WITH') == 'XMLHttpRequest'
```
	
 


1. csrftoken
	- 有两种方式
		- 在请求头中设置`csrftoken`及对应的value
		- 在请求体中设置`&csrfmoddlewaretoken=xxxxxxxx`
			- 对应的值可以使用字符串拼接嵌入到请求体中

```javascript
var ele_csrf = document.getElementsByName('csrfmoddlewaretoken')[0]
xmlHttp.send("name=ziawang&age=18&csrfmoddlewaretoken="+ele_csrf.value)
``` 


2. POST请求需要请求头中设置Content-Type
- POST请求发送数据的时候，需要使用`setRequestHeader("Content-Type", "application/x-www-form-urlencoded")`

```javascript
var xmlHttp = createXMLHttpRequest();
var btn = document.getElementByTagName("button");

btn.onclick = function(){
	xmlHttp.open('post', '/home/');
	xmlHttp.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
	var ele_csrf = document.getElementsByName('csrfmoddlewaretoken')[0];
	xmlHttp.send("name=ziawang&age=18&csrfmoddlewaretoken="+ele_csrf.value);
	
	xmlHttp.onReadyStateChange = function(){
		if (this.readState == 4 && this.status == 200){
			consol.log(this.responseText);
		}else if(this.status == 4 && this.status == 500){
			alert("服务端出错啦");
		}
	}
}
```


3. 当Content-Type为application/json时

```
var xmlHttp = createXMLHttpRequest();
var btn = document.getElementByTagName("button");

btn.onclick = function(){
	xmlHttp.open('post', '/home/');

	xmlHttp.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
	var csrf_value = document.getElementByName("csrfmiddleware").value
	xmlHttp.setRequestHeader("X-CSRFToken", "csrf_value");
	
	var ele_csrf = document.getElementsByName('csrfmoddlewaretoken')[0];

	xmlHttp.send('{"name": "ziawang", "age": 18}');
	
	xmlHttp.onReadyStateChange = function(){
		if (this.readState == 4 && this.status == 200){
			consol.log(this.responseText);
		}else if(this.status == 4 && this.status == 500){
			alert("服务端出错啦");
		}
	}
}
```

## 举例

###### 在请求体中存放csrf键值对

```javascript
    function createXMLHttpRequest() {
        var xmlHttp;
        // 适用于大多数浏览器，以及IE7和IE更高版本
        try {
            xmlHttp = new XMLHttpRequest();
        } catch (e) {
            // 适用于IE6
            try {
                xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
            } catch (e) {
                // 适用于IE5.5，以及IE更早版本
                try {
                    xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
                } catch (e) {
                }
            }
        }
        return xmlHttp;
    }


    var xmlHttp = createXMLHttpRequest();

    $("#submit").click(function () {
       xmlHttp.open('post', '/index/');
       xmlHttp.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
        var csrfmiddlewaretoken = document.getElementsByName('csrfmiddlewaretoken')[0].value;

       xmlHttp.send(`name=ziawang&age=18&csrfmiddlewaretoken=${csrfmiddlewaretoken}`);

       xmlHttp.onreadystatechange = function () {
           if (this.readState == 4 && this.state == 200){
               console.log(this.responseText);
           }else if(this.readyState == 4 && this.state == 500){
               console.log('服务端出错啦');
           }
       }
    });
```


```python
# views.py
def index(request):
    if request.method == 'POST':
        print(request.body)
        print(request.POST)
        return HttpResponse("ok")
    return render(request, 'index.html')

# ============== 结果 ==============
b'name=ziawang&age=18&csrfmiddlewaretoken=pwdZVLfOo12owC4zo6RHGdwJh0aj2bTKRWtpLdrm9Z2p95XC10DA27PUjeJxuAKU'
<QueryDict: {'name': ['ziawang'], 'age': ['18'], 'csrfmiddlewaretoken': ['pwdZVLfOo12owC4zo6RHGdwJh0aj2bTKRWtpLdrm9Z2p95XC10DA27PUjeJxuAKU']}>


```



###### 在请求头中设置csrf键值对

```javascript
    function createXMLHttpRequest() {
        var xmlHttp;
        // 适用于大多数浏览器，以及IE7和IE更高版本
        try {
            xmlHttp = new XMLHttpRequest();
        } catch (e) {
            // 适用于IE6
            try {
                xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
            } catch (e) {
                // 适用于IE5.5，以及IE更早版本
                try {
                    xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
                } catch (e) {
                }
            }
        }
        return xmlHttp;
    }


    var xmlHttp = createXMLHttpRequest();

    $("#submit").click(function () {
       xmlHttp.open('post', '/index/');
       xmlHttp.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
        var csrfmiddlewaretoken = document.getElementsByName('csrfmiddlewaretoken')[0].value;
       xmlHttp.setRequestHeader('X-CSRFToken', csrfmiddlewaretoken);

       xmlHttp.send(`name=ziawang&age=18`);

       xmlHttp.onreadystatechange = function () {
           if (this.readState == 4 && this.state == 200){
               console.log(this.responseText);
           }else if(this.readyState == 4 && this.state == 500){
               console.log('服务端出错啦');
           }
       }
    });

```

```python
# views.py
def index(request):
    if request.method == 'POST':
        print(request.body)
        print(request.POST)
        return HttpResponse("ok")
    return render(request, 'index.html')

# ======= 结果 ===============
b'name=ziawang&age=18'
<QueryDict: {'name': ['ziawang'], 'age': ['18']}>
```