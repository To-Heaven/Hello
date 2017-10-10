# 获取页面节点
- JavaScript中获取页面节点是通过操作document对象来实现的。
- 主要有两种方式
	- 利用DOM树获取
	- 利用css选择器获取

## 通过DOM树获取
- 注意
	- 以下方法适用于所有标签对象，可以是document，也可以是通过document得到的标签对象
	- 比如`ele = document.getElementsByClass('animal').getElementByTagName('p')`
		- 获取页面中类为animal的p元素

 
### 方法
#### 返回单个节点对象
- ele.getElementById(str_id)
	- 返回节点以下级别的子节点中id为str_id的对象

#### 返回节点对象组成的列表
- ele.getElementsByTagName(str_tag_name)
	- 返回标签名为str_tag_name的节点对象组成的列表
- ele.getElementsByName('name_value')
	- 返回标签name属性为'name_value'的标签都想组成的列表

- ele.getElementsByClass(str_class)
	- 返回类为str_class的节点对象组成的列表 

### 属性
- ele.parElement
	- 返回节点的父节点对象
- ele.children
	- 返回一个子节点对象组成的列表
	- 节点的属性
	- 返回该节点下的子节点（不包含子节点的子节点。。。）
- ele.firstElementChild
	- 返回子节点列表中的第一个子节点
- ele.lastElementChild
	- 返回节点列表中最后一个子节点 
- nextElementSibling
	- 下一个兄弟标签元素
- previousElementSibiling
	- 上一个兄弟标签原元素

## 通过css选择器获取
> [CSS选择器](http://www.ziawang.com/page/css/selector.html)

- document.querySelector(str_selector)
	- 返回页面中第一个选择器为`str_selector`的节点对象，选择器通常为ID选择器


- document.querySelectorAll(str_selector1, str_selector2, ...)
	- 返回选择器为`str_selector1`或`str_selector2`的节点对象组成的列表
	- 注意：
		- 当`str_selector1`与`str_selector2`顺序反过来时，结果可能不一样


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="test-div">
    <div class="c-red">
        <p id="test-p">JavaScript</p>
        <p>Java</p>
    </div>
    <div class="c-red c-green">
        <p>Python</p>
        <p>Ruby</p>
        <p>Swift</p>
    </div>
    <div class="c-green">
        <p>Scheme</p>
        <p>Haskell</p>
    </div>
</div>
<script>
    var js = document.getElementById('test-p');
    console.log(js);

    var arr = document.getElementsByClassName('c-red c-green');
    console.log(arr)
    for (let i of arr){
        console.log(i);
    }

    var haskell = document.getElementById('test-div').lastElementChild.lastElementChild
    console.log(haskell)
</script>
</body>
</html>
</html>
```