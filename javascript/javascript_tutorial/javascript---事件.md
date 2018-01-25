# DOM 事件

## 事件的两种触发方式
- 直接在HTML标签中触发事件

```html
<form action="">
    <p><input type="button" value="点我" onclick="alert('在HTML标签中触发事件')"></p>
</form>
```

- 在JavaScript中被触发

```javascript
<form action="">
    <p><input type="button" value="点我" id="test"></p>
</form>
<script>
    var btn_test = document.getElementById('test');

    btn_test.onclick = function () {
        alert('在JavaScript中触发事件');
    }
</script>
```

## 事件委派
- 所谓事件委派其实就是通过对一个父节点进行事件监听，当存在满足对应条件的子节点时，该子节点就会执行响应的事件。
- 为什么要用事件委派？
	- 假设现在有一个后台管理界面，界面上有一个列表，列表中存放的是一个个用户，并且每一个用户所在的行都有一个“编辑”的按钮，用来编辑用户的所有相关信息，如果我们不使用事件委派，那么我们就必须为每一个按钮添加一个点击事件，触发编辑功能，当有很多用户的时候，这样做显然是不行的，这个时候，事件委派就排上了用场。

#### JavaScript中的事件委派
- 步骤
	1. 首先你要获取要添加事件监听的父节点dom对象
	2. 然后，使用父节点对象的setEventListener('event_name', callback_func, useCapture)
		- 第一个参数是你要监听的事件类型
		- 第二个参数是该事件触发时的回调函数
		- 第三个参数是一个布尔值
			- 当为false的时候使用事件冒泡传播。默认为false
			- 当为true的时候，使用捕获传播 

- 举例

```html
<ul id="resources">
  <li><a href="http://developer.mozilla.org">MDN</a></li>
  <li><a href="http://html5doctor.com">HTML5 Doctor</a></li>
  <li><a href="http://html5rocks.com">HTML5 Rocks</a></li>
  <li><a href="http://beta.theexpressiveweb.com/">Expressive Web</a></li>
  <li><a href="http://creativeJS.com/">CreativeJS</a></li>
</ul>
```


```javascript
var resources = document.querySelector('#resources'),
log = document.querySelector('#log');
resources.addEventListener('mouseover', showtarget, false);
function showtarget(ev) {  
  var target = ev.target;
  if (target.tagName === 'A') {
    log.innerHTML = 'A link, with the href:' + target.href;
  }
  else if (target.tagName === 'LI') {
    log.innerHTML = 'A list item';
  }
  else if (target.tagName === 'UL') {
    log.innerHTML = 'The list itself';
  }}
```

## 点击事件
- onclick
	- 在元素被点击时触发
- ondbclick
	- 在元素被双击时触发
- 应用：
	- 全选/反选/取消

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<button id="selectall">全选</button>
<button id="selectreverse">反选</button>
<button id="selectcancel">取消</button>

<table style="border: 1px solid black">
    <tr>
        <td><input type="checkbox" class="check"></td>
        <td>11111</td>
        <td>11111</td>
        <td>11111</td>
    </tr>

    <tr>
        <td><input type="checkbox" class="check"></td>
        <td>11111</td>
        <td>11111</td>
        <td>11111</td>
    </tr>

    <tr>
        <td><input type="checkbox" class="check"></td>
        <td>11111</td>
        <td>11111</td>
        <td>11111</td>
    </tr>

    <tr>
        <td><input type="checkbox" class="check"></td>
        <td>11111</td>
        <td>11111</td>
        <td>11111</td>
    </tr>
</table>


<script>
    var selectall = document.getElementById('selectall');
    var selectreverse = document.getElementById('selectreverse');
    var selectcancel = document.getElementById('selectcancel');
    var arr_check = document.getElementsByClassName('check');

    // 分别在多个函数中实现

    selectall.onclick = function () {
        for (var ele of arr_check) {
            ele.checked = true;
        }
    };

    selectreverse.onclick = function () {
        for (var ele of arr_check) {
            if (ele.checked)
                ele.checked = false;
            else
                ele.checked = true;
        }
    };

    selectcancel.onclick = function () {
        for (var ele of arr_check) {
            ele.checked = false;
        }
    };

    // 也可以将上述事件触发的函数封装到一个函数中
    
</script>
</body>
</html>
```


## 加载事件
- onload
	- 在页面加载结束时触发
	- 当JavaScript读取到onload事件的时候，不会执行其中的代码，而是等页面加载结束之后再触发事件




## 提交表单
- JavaScript表单提交有两种方式
	1. 使用form节点对象的`f.submit()`方法提交
		- 浏览器默认是点击`<input type='submit'>`或`<button type='submit'>`时提交表单。当通过事件触发`f.submit()`方法的时候也可以提交表单，但是这样明显扰乱了表单信息正常提交
	2. 响应form表单对象自身的`onsubmit`事件
		- 使用这种方法提交表单数据时，form中应该有`type='submit'`属性的`<input>`或者`<button>`标签，这是浏览器对form表单提交的默认方式。通过`onsubmit`事件可以对默认提交进行控制，并再触发的目标函数中对form表单中的数据进行处理和逻辑判断（比如是否要提交）。


```html
<!--使用f.submit()方法提交-->
<form id="test-form">
    <input type="text" name="test">
</form>
<button type="button" onclick="doSubmitForm()">点击提交</button>

<script>
    function doSubmitForm() {
        var form = document.getElementById('test-form');
        form.submit();					// 使用form.submit()提交
    }
</script>
```

- 使用`f.onsubmit`事件触发默认提交有两种方式
	- 当`f.onsubmit`事件对应的回调函数返回值为`true`的时候，会触发默认提交，否则不会提交
	- 在JavaScript中声明`f.onsubmit`事件
	- 在HTML中声明`f.onsubmit`事件


```html
<!--在JavaScript使用f.onsubmit事件触发提交-->
<form id="test-form">
    <p>user : <input type="text" name="user"></p>
    <p>age : <input type="text" name="age"></p>
    <button type="submit">Submit</button>
</form>

<script>
    var form = document.getElementById('test-form');
    var input = form.firstElementChild.firstElementChild;
    console.log(input)
    form.onsubmit = function () {
        if (input.value){
            return true;
        } else {
            return false;
        }
    }
</script>
```

```javascript
<!--在HTML使用f.onsubmit事件触发提交-->
<form id="test-form" onsubmit="return doSubmit()">
    <p>user : <input type="text" name="user"></p>
    <p>age : <input type="text" name="age"></p>
    <button type="submit">Submit</button>
</form>
<script>
    var form = document.getElementById('test-form');
    var input_user = form.firstElementChild.firstElementChild;

    function doSubmit() {
        if (input_user.value){
            return true;
        } else {
            return false;
        }
    }
</script>
```

## 事件传播（事件冒泡）
- 当触发子节点的某个事件时，如果父节点有同样类型的事件，那么父节点的事件也会被触发

```html
<body>
<div class="outer" style="width: 300px; height: 300px;background-color: red">
    <div class="inner" style="width: 200px; height: 200px;background-color: yellow"></div>
</div>
</body>
<script>
    var outer = document.getElementsByClassName('outer')[0];
    var inner = document.getElementsByClassName('inner')[0];
    outer.onclick = function () {
        alert("this is outer's onclick ")
    };

    inner.onclick = function () {
        alert("this is inner's onclick ")
    }

    // 当点击inner区域的时候，会将事件传播到inner的父级，即会触发父级的事件

</script>
```

- 解决方法
	- 给子节点的事件对象event调用`sub.stopPropagation()`方法
		- 注意，要在触发函数中将事件对象作为实参传入

```html

<script>
    var outer = document.getElementsByClassName('outer')[0];
    var inner = document.getElementsByClassName('inner')[0];
    outer.onclick = function () {
        alert("this is outer's onclick ");
    };

    inner.onclick = function (event) {
        alert("this is inner's onclick ");
        event.stopPropagation();
    }
    
</script>
```








## 焦点事件
- `onfocus` 和 `onblur`
	- 用于`<input type='text'>`文本框等能够捕获光标的元素
	- onfocus就是光标在text内
	- onblur就是光标（鼠标）移动到文本输入框之外


```html
<!--模拟输入框placeholder属性-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="">
    <input type="text" name="user">
</form>

<script>
    var input_test = document.getElementsByTagName('input')[0];

    input_test.value = 'username';

    input_test.onfocus = function () {
        if (input_test.value == 'username')
            input_test.value = '';
    };

    input_test.onblur = function () {
        if (!input_test.value.trim())
            input_test.value = 'username';
    }

    // 或者这样写
    //    input_test.onblur = function () {
    //        if (input_test.value.trim() == '')
    //            input_test.value = 'username';
    //    }

</script>
</body>
</html>
```

## 按键（键盘）事件
- `onkeydown`、`onkeyup`和`onkeypress`
	- onkeydown即按下键pattern之后会触发的事件
	- onkeypress是按下键至松开的过程中触发的事件
	- onkeyup是按键被松开后会触发的事件
	- 使用event的code属性可以对键进行判断（比如enter键）

```html


```



## 注意：event变量
- 事件对应的函数会有一个event变量作为参数传入，这个形参的名称在定义的时候不必须是`event`。
- event的属性
	- event.code
		- 键盘上每一个按键对应的状态码，用于在“按键事件”中对键的判断（比如判断是否是enter键）
	- event.target
		- 返回一个节点对象，用于事件委派
	- **待补充**



## 鼠标事件
- `onmousedown`、`onmousemove`、`onmouseout`、`onmouseover`、`onmouseleave`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .test{
            width: 200px;
            height:70px;
            background-color: #2aabd2;
        }

        .hide{
            display: none;
        }
    </style>
</head>
<body>
<div id="outer" style="border: 1px solid rebeccapurple;width: 300px">
    <div id="inner1" class="test" style="background-color: #2b542c">
        <p>onmouseover test</p>
    </div>
    <ul class="test hide" id="inner2">
        <li><a href="">www.ziawang.com</a></li>
        <li><a href="">www.baidu.com</a></li>
        <li><a href="">www.python.org</a></li>
    </ul>
</div>

<script>
    var ul = document.getElementById('inner2');
    var div_inner = document.getElementById('inner1');
    var div_outer = document.getElementById('outer');

    div_inner.onmouseover = function () {
        ul.classList.remove('hide');
    }

    div_outer.onmouseleave = function () {
        ul.classList.add('hide');
    }
    
</script>
</body>
</html>
```



## 内容事件
- `onchange`
	- 内容被改变时触发的事件。
	- 注意：**这种改变不是input框中的内容被修改后立马触发，而是input中内容被修改并且光标离开input框的时候才会触发**
	- 应用：
		- 二级联动
		- 头像预览

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>


<form action="">
    <select name="" id="select_province">
        <option value="">选择省份</option>
        <option value="北京">北京</option>
        <option value="河南">河南</option>
    </select>

    <select name="" id="select_city">
        <option value="">选择城市</option>
    </select>

    <select name="" id="select_distinct">
        <option value="">选择区县</option>
    </select>
</form>

<script>
    var province_map = {
        北京: ['朝阳', '昌平'],
        河南: ['郑州', '洛阳']
    };

    var city_map = {
        朝阳: ['朝a', '朝b'],
        昌平: ['昌a', '昌b'],
        郑州: ['郑a', '郑b'],
        洛阳: ['洛a', '洛b']
    };

    var province = document.getElementById('select_province');
    var city = document.getElementById('select_city');
    var distinct = document.getElementById('select_distinct');

    province.onchange = function () {
        var arr_city = province_map[province.value];

        console.log(city.children)
        city.options.length = 1;			// 初始化select中的option选项数量，每一次选择上级选项时，下级选项应该初始化
//        city.children.length = 1;

        for (let city_name of arr_city) {
            var option = document.createElement('option');
            option.innerText = city_name;
            city.appendChild(option);
        }
    }

    city.onchange = function () {
        var arr_distinct = city_map[city.value];

        distinct.options.length = 1;
        
        for (let distinct_name of arr_distinct) {
            var option = document.createElement('option');
            option.innerText = distinct_name;
            distinct.appendChild(option);
        }
    }


</script>
</body>
</html>
```




## 选中事件
- `onselect`
	- 文本框中的内容被选中时触发的事件

