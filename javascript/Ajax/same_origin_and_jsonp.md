# 同源策略与jsonp


## 同源策略与Ajax请求
#### 同源策略
- **浏览器最基本、最核心的安全功能**
- 所谓同源，就是两个页面具有相同的scheme协议、IP地址和端口，只要有其中一个条件不同，那么这两个页面就不是同源的。
	- 以`http://www.ziawang.com/ajax/`为例
	
```
// 同源的页面路径为

http://www.ziawang.com/python/

http://www.ziawang.com/ajax/jsonp/

```

```
// 不同源的页面路径为

https://www.ziawang.com/ajax/

http://www.ziawang.cn/ajax/

http://www.ziawang.com:88/ajax/
```

- 同源策略限制是浏览器用来隔离潜在恶意文件的关键安全机制
	- 保证用户信息安全，防止恶意网站窃取数据


#### IE浏览器对端口的例外
- IE一直以来都是浏览器中的奇葩，在同源策略中，对于两个具有相同scheme、IP地址但是不同端口的页面，IE浏览器会认为这两个页面来自同一个源。


#### 同源策略限制了什么
- 对于非同源的页面来说，通常限制了跨域的读取操作

###### 1. 不允许跨域读操作
1. cookie、localstorage、indexedDB
	-  IndexedDB 是一种低级API，用于客户端存储大量结构化数据(包括, 文件/ blobs)，该API使用索引来实现对该数据的高性能搜索
2. 页面的DOM对象
3. 不能向非同源的页面对应路径发送**Ajax请求**

- 可以内嵌资源巧妙的进行读取操作
	- **调用内嵌脚本的方法**（常用）
	- 读取嵌入图片的高度和宽度

#### 同源策略不限制什么
- 对于非同源对的页面来说，通常允许跨域写入操作和跨域资源嵌入操作

###### 1. 跨域写入操作
1. 链接links
2. 重定向
3. **表单提交**

###### 2.跨域资源嵌入
1. 图片等媒体资源(img, audio, video)
2. a标签超链接
3. JavaScript脚本script标签src
4. css脚本link标签的href
5. <object>, <embed> 和 <applet>的插件。
6. @font-face引入的字体。一些浏览器允许跨域字体（ cross-origin fonts），一些需要同源字体（same-origin fonts）
7. <frame>和<iframe>载入的任何资源
	- 站点可以设置X-Frame-Options消息头来组织这种形式的跨域交互
6. and so on ...

#### 更改页面的源
- 页面的源可以看作每一个页面的属性，那么我们可以使用修改属性的方法来修改页面的源，但是修改是有限制的，我们只能通过`document.domain`修改页面的源为当前页面的域`www.ziawang.com`或者当前与的超级域`ziawang.com`，**赋值的时候必须指定端口号，否则保存在浏览器中的端口会被设置为null**
	- `document.domain = "ziawang.com:80"`


## 如何阻止跨源访问
1. 阻止跨域写操作————csrf token标记页面的跨站写操作
2. 阻止跨域嵌入————保证资源不能是可嵌入的上述7中格式
3. 阻止跨站资源读取————保证资源是不可嵌入的

## Ajax如何允许跨站资源访问
- 实现Ajax跨域访问资源


#### 1. jsonp
- 利用`<script>`标签可以跨域资源嵌入的特性
	- 动态添加和删除script标签

- jsonp的本质
	- 在前端js中创建一个回调函数，服务端使用json返回该函数的调用并且传入参数

###### 1.jQuery实现
- 方式一，使用`$.getJSON(url,[data],[callback])`

```
   function f(){
          $.getJSON("http://127.0.0.1:7766/SendAjax/?callbacks=?",function(arg){
            alert("hello"+arg)
        });
    }
```

http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html

- 方式二，使用`$.ajax`

```
    function f(){
          $.ajax({
                url:"http://127.0.0.1:7766/SendAjax/",
                dataType:"jsonp",
                jsonp: 'callbacks',
                jsonpCallback:"SayHi"
           });

       }

    function SayHi(arg){
                alert(arg);
            }
```

#### WebSocket	


#### CORS



## 跨源数据存储的访问
- 对于一个浏览器中多个不同的源来说，各自都有一个互相隔离的源存储空间，每一个源的存储空间中存放着与该源相关的localstorage和indexedDB数据
	- **一个源中的script脚本不能对其他源中的数据进行操作**


#### window.name
- **window.name可以用来存储临时数据，并且可以实现跨源访问**，只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它

###### 优缺点
- 优点
	- 存储量大

- 缺点
	- 缺点是必须监听子窗口window.name属性的变化，影响网页性能


###### 设置和读取
- 设置`window.name = xxx;`
- 读取`窗口DOM对象.contentWindow.name`

#### cookie
- **设置cookie时，你可以使用Domain，Path，Secure，和Http-Only标记来限定其访问性。读取cookie时，不会知晓它的出处。尽管使用安全的https连接，任何可见的cookie都是使用不安全的连接设置的**
