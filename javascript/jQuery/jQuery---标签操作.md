# 标签操作


## 增加标签

### 通过父级别添加
- 使用`$(selector).html([str_val])`
- 使用`$(selector).append(content | fn)`
	- content
		- string字符串
			- 字符串可以使用拼接的方式，将变量名和多个字符串连接起来
		- 标签元素
		- jQuery对象
			- 注意：append参数为其他已存在的jQuery对象的时候，该jQuery对象对应的标签会现在DOM树中移除，再添加到指定位置
	- fn
		-  function(index, html)
	- 向每个匹配的元素内部追加内容。
		- 内容可包含标签，会对指定元素执行appendchild方法

```html
<body>
<p id="p1"></p>
<p id="p2"></p>
<p id="p3"></p>
<p id="p4"></p>
</body>
<script>
    var p1 = $('#p1');
    p1.append('<a>this is <strong>p1</strong></a>');    // 添加string

    var p2 = $('#p2');
    var inner_eles = document.createElement('a');
    inner_eles.innerHTML = 'this is <strong>p2</strong>';
    p2.append(inner_eles);    // 添加dom


    var p3 = $('#p3');
    p3.append(p2);          // p2将会移动到p3内

    var p4 = $('#p4');
    p4.append(function () {
        return 'this is <strong>p4</strong>'
    })

</script>
```


> content: String of html, Element, jQuery, Function

- `$(selector).appendTo(content)`
	- 将匹配到的元素添加到指定的content元素内部，与append相反


- `$(selector).prepend(content)`
	- 使用和append相同，但是会把dom对象content添加到匹配元素内部的最前面

- `$(selector).prepend(content)`
	- 将匹配到的元素添加到指定content元素内部的前面



### 通过同级别添加
- `$('selector').after(content)`
	- 使用和append相同，将匹配的对象添加到同级别content标签后面
- `$('selector').before()`
	- 使用和append相同，将匹配的对象添加到同级别标签前面

- `$(selector).insertAfter(content)`
	- 将匹配到的所有元素插入到指定content元素的后面

- `$(selector).insertBefore(content)`
	- 将匹配到的所有元素插入到指定content元素的前面

## 删除标签
- 直接调用jQuery对象的remove方法就可以将该jQuery对象及其子元素全部删除
- `$(selector).remove()`
	- 删除匹配到的元素及其子元素，返回值为该元素的jQuery对象，建议不要通过整个返回值appendTo到其他标签内

- `$(selector).empty()`
	- 清空匹配到元素的所有子元素
	
- `$(selector).decay()`
	- 剪切匹配到的元素，并返回该元素的jQuery对象，使用该对象的appendTo方法将其添加到任意位置

## 显示和隐藏标签
- 显示隐藏的标签
	- `$('selector').show()`
	
- 隐藏显示的标签
	- `$('selector').hide()` 


## 包裹标签
- `$(selector).wrap(element)`
	- 将匹配到的元素用指定的element**分别**包裹起来
	
- `$(selector).wrapAll(element)`
	- 将匹配到的元素用指定的element作为一个整体包裹起来

- `$(selector).unwrap()`
	- 没有参数
	- 将匹配到元素的外层包裹去除

- `$(selector).wrapInner(element)`
	- 将每一个匹配元素的子内容用一个html结构包裹起来

## 替换所有标签
> content参数: String of html, Element, jQuery, Function

- `$(selector).replaceWith(content)`
- `$(selector).replaceAll(content)`

## 克隆
- `$(select).clone([deepevent=true])`
	- 克隆匹配的DOM元素并且返回这些克隆的副本。
	- deepevent=true
		- 将对象绑定的事件也拷贝