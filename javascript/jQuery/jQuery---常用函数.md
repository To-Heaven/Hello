## jQuery.each(object[, callback])
- object： 要被each遍历的对象
- callback： 遍历得到的每个对象要调用的函数（回调函数）
	- callback可以传入两个参数，分别代表对象的属性和属性值
- 调用方式
	- 库调用`$.each($(selector), function)`
	- 对象调用`$(selector).each(function)`
- 在遍历过程中实现惰性运算
	- 在function中return false

#### 在each中使用this
- **在each函数内的回调函数使用this关键字的时候，this代表的是当前被遍历出来的DOM对象**

```html
# 登陆校验

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录校验</title>
    <link rel="stylesheet" href="bootstrap-3.3.7/css/bootstrap.min.css">
    <style>
        .c1 {
            margin-top: 100px;
        }
    </style>
</head>
<body>
<div class="container">
    <div class="row">
        <div class="col-md-4 c1 col-md-offset-4">
            <form class="form-horizontal">
                <div class="form-group">
                    <label for="inputEmail3" class="col-sm-2 control-label">Email</label>
                    <div class="col-sm-10">
                        <input type="email" class="form-control" id="inputEmail3" placeholder="Email" aria-describedby="helpBlock1">
                        <span id="helpBlock1" class="help-block"></span>
                    </div>

                </div>
                <div class="form-group">
                    <label for="inputPassword3" class="col-sm-2 control-label">密码</label>
                    <div class="col-sm-10">
                        <input type="password" class="form-control" id="inputPassword3" placeholder="Password" aria-describedby="helpBlock2">
                        <span id="helpBlock2" class="help-block"></span>
                    </div>
                </div>
                <div class="form-group">
                    <div class="col-sm-offset-2 col-sm-10">
                        <div class="checkbox">
                            <label>
                                <input type="checkbox"> Remember me
                            </label>
                        </div>
                    </div>
                </div>
                <div class="form-group">
                    <div class="col-sm-offset-2 col-sm-10">
                        <button type="submit" class="btn btn-default">Sign in</button>
                    </div>
                </div>
            </form>
        </div>
    </div>
</div>

<script src="jquery-3.2.1.js"></script>
<script src="bootstrap-3.3.7/js/bootstrap.min.js"></script>
<script>

    $("form button").on("click", function () {
       // 先清空状态
        $("form .form-group").removeClass("has-error");
        $("form span").text("");
        // each循环
    $("form input").each(function () {
        // 做判断
        if ($(this).val().length === 0){
            // 给他的爸爸加has-error
            $(this).parent().parent().addClass("has-error");
            // 给span写内容
            // 找到我这是什么input  --> 找到对应的label的文本内容
            var name = $(this).parent().prev().text();
            $(this).next().text(name + "不能为空");
            return false;
        }
    });
        return false;
    });

</script>
</body>
</html>
```




## jQuery.is(selector|obj|ele|fn)
- 根据比较选择器、DOM对象或jQuery对象匹配到的元素与jQuery对象是否符合来返回布尔值
- 也可以通过传入回调函数来返回bool值，回调函数中一般返回的是一个逻辑表达式，逻辑表达式的值对应布尔值

```html


```


## 过滤方法filter(filter)
- 可以对jQuery对象进行筛选，只留下被filter过滤后剩下的元素


## 在元素上存放数据并返回jQuery对象
- `$(selector).data([, key][, value])`
	- key： 要存储的数据名
	- value： 要存储的数据（可以是任意类型，比如jQuery对象）
	- 注意
		- 当只提供key时，返回存储的对象
		- 当提供所有参数时，在匹配对象上存放数据

- `$(selector).removeData([name | list])`
	- 从匹配元素中删除之前添加的数据


## clone([even[, deepEven]])
- 克隆由选择器匹配到的DOM元素并选中这些克隆得到的副本
- 参数
	- even，布尔值，当要连同被克隆对象的绑定事件也一起克隆的时候，将此参数设置为true即可
	- deepEven，也是布尔值，当指定的时候，会连同克隆元素的子元素一起复制并同时克隆绑定的事件
