# 动画


## 显示与隐藏
- 显示`$(selector).show([speed][, easing][, function])`
	- 显示匹配到的元素
	- parameters
		- speed: 动画速度
			- 使用可选的字符串`slow, normal, dast`
			- 使用数字（数字越大，速度越慢，数字表示动画运行完所需要的总时长）
		- easing: 切换效果（字符串）
			- swing
			- linear
		- function
			- 动画执行结束所需要的时间 

-隐藏`$(selector).hide([speed][, easing][, function])`
	- 隐藏匹配到的元素
	
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../jquery-3.2.1.js"></script>
</head>
<body>
<p id="p1">ppppppppppppppppppppppp哈哈哈哈哈哈哈哈哈哈</p>
<button id="bOn">显示</button>
<button id="bOff">隐藏</button>

<script>
    var p = $('#p1');

    $('#bOn').click(function () {
        p.show(1000, 'linear', function () {
            alert('p1动画运行结束')
        })
    });

    $('#bOff').click(function () {
        p.hide()
    });

</script>
</body>
</html>
```
	 
- 自动切换 `$(selector).toggle([speed][, easing][, function])`
	- 如果匹配到的元素时隐藏的，就切换到显示，如果匹配到的元素时显示的，那么就切换成隐藏的
	- 参数和hide/show相同

```html
<script>
$('button').click(function () {
    p.toggle(1000, 'swing', function () {
        alert('动画运行结束')
    })
})
</script>
```


## 淡入淡出
- 淡出`$(selector).fadeOut([speed],[easing],[fn])`
	- 参数和hide相同
	- 这个动画只调整元素的不透明度，不会改变元素的高度和宽度产生	

- 淡入`$(selector).fadeIn([speed],[easing],[fn])`
	- 这个动画只调整元素的不透明度，不会改变元素的高度和宽度产生 

- 淡入淡出自动切换`$(selector).fadeToggle([speed],[easing],[fn])`
	- 这个动画只调整元素的不透明度，不会改变元素的高度和宽度产生


## 自定义动画
- `$(selector).animate(paras[,speed],[easing],[fn])`
	- paras：以map对象格式显示，包含匹配对象最终显示格式（属性）
	- 其他参数和hide相同
	
```html
<body>
<div id="test-canton"></div>
<button>点击触发动画</button>
<script>
    var target = $('#test-canton');

    $('button').click(function () {
        target.animate({
            height: '200px',
            width: '200px',
            borderColor: '#336699',
            borderWidth: '2px'
        }, 1000)
    })

</script>
```
 