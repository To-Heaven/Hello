# 事件




## 加载事件与加载顺序
- 在文件中，如果匹配对象的相关调用出现在了匹配元素创建之前，那么相关的调用并不会被执行。因为调用该对象的方法时，该对象并不存在。
- 解决这种情况的常用方法是使用document对应jQuery对象的`ready`方法。有三种调用方式
	- `$(document).on('ready', function(){...})`
	- `$(document).ready(function(){...})`
	- `$(function(){...})` 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../jquery-3.2.1.js"></script>

</head>
<body>
<script>
    $(function () {
        $('button').click(function () {
            alert(1111)
        })
    })
</script>

<form action="">
    <input type="text" placeholder="...">
    <button>点点点</button>
</form>

</body>
</html>
```






## 事件中使用this
- 在触发事件的回调函数中，this代表触发事件的对象


```javascript
$('.container form button[type="submit"]').click(function () {
    alert(this)
})

// this代表$“('.container form button[type="submit"]')”匹配的对象
```



## jQuery的事件委派
- 什么是事件委派
	- 将原来加在子元素身上的事件绑定在父类身上，通过父类来触发子类的事件
- 为什么要用事件委派
	- 如果给每一个子元素都绑定事件，是非常耗费资源和性能的
	- 如果只给每一个子元素绑定事件，对于后期使用js动态的子元素，将不会拥有这个事件，因此最好的方法是将事件绑定到父元素身上，通过父元素来触发子元素的事件

- 使用`on(events, [selector, ], [data], fn)`实现
	- events
		- 一个字符串，用于指定事件类型，可以是多个 `'click mouseleave'`
	- selector
		- 选择器，匹配到的子元素才能触发events事件
	- fn
		- 必须，事件触发时执行的函数，通常将event作为参数传入，用于定位具体子元素（当相同类型子元素有很多的时候，使用e.target配合jQuery对象的转换，可以定位到任何一个触发事件的对象）
		

```html
<table id="ele_table">
    <!--头-->
    <thead>
    <tr>
        <th>姓名</th>
        <th>年龄</th>
        <th>性别</th>
        <th>操作</th>
    </tr>
    </thead>
    <!--体-->
    <tbody>
    <tr>
        <td>ziawang</td>
        <td>22</td>
        <td>male</td>
        <td>
            <button class="editBtn">编辑</button>
            <button class="delBtn">删除</button>
        </td>
    </tr>
    </tbody>
</table>

<script>
	// 实现删除
    $('tbody').on('click', 'td>button', function (e) {
		// 通过jQuery对象和DOM对象的相互转换，定位元素
        if (e.target.className === 'btn btn-danger delBtn')
            $(e.target).parent().parent().remove();
    })
</script>
``` 


## 事件参数
- 所有的事件内执行任务的函数都可以传入一个参数作为事件参数，通过这个参数可以获取该jQuery对象的其他相关属性

```htnl
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../jquery-3.2.1.js"></script>
</head>
<body>
<ul id="u1">
    <li>这是li : 1</li>
    <li>这是li : 2</li>
    <li>这是li : 3</li>
    <li>这是li : 4</li>
    <li>这是li : 5</li>
</ul>

<script>
    $('li').click(function (e) {
        $(e.target).text('duang ')
    })

</script>
</body>
</html>
```




## 滚动事件
> 滚动指定元素时发生会触发的事件

- `$(secletor).scroll([data], function)`
	- data : 传入供function处理
	- function：触发scroll时间时，执行的任务
	
- 常用
	- `$(window).scroll([data], function)`
	- 表示当浏览器滚动条滚动时触发的事件

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../jquery-3.2.1.js"></script>
    <style>
        .a{
            margin-top: 100px;
        }
        .hide{
            display: none;
        }
        #btn{
            position: fixed;
            right: 30px;
            bottom: 30px;
        }
    </style>
</head>
<body>
<p class="a">1</p>
<p class="a">2</p>
<p class="a">3</p>
<p class="a">4</p>
<p class="a">5</p>
<p class="a">6</p>
<p class="a">7</p>
<p class="a">8</p>
<p class="a">9</p>
<p class="a">10</p>
<p class="a">11</p>
<button id="btn">点击回到顶部</button>

<script>
    $(window).scroll(function () {
            if ($(window).scrollTop() < 500 ){
                $('#btn').addClass('hide');
            } else {
                $('#btn').removeClass('hide');
            }
    })

    $('#btn').click(function () {
        $(window).scrollTop(0)
    })
</script>
```  


## 移除事件
- `$(selector).off('event_type')`
	- 移除匹配元素的事件
	- event_type 为事件的名称

```html

```

