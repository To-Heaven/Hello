## jQuery.each(object[, callback])
- object： 要被each遍历的对象
- callback： 遍历得到的每个对象要调用的函数（回调函数）
	- callback可以传入两个参数，分别代表对象的属性和属性值
- 调用方式
	- 库调用`$.each($(selector), function)`
	- 对象调用`$(selector).each(function)`
- 在遍历过程中实现惰性运算
	- 在function中return false

```html
var arr_num = ['a', 'b', 'c', 'd', 'e'];
$.each(arr_num, function (e, v) {
    console.log(e);
    console.log(v);
})
```


## jQuery.is(selector|obj|ele|fn)
- 根据比较选择器、DOM对象或jQuery对象匹配到的元素与jQuery对象是否符合来返回布尔值
- 也可以通过传入回调函数来返回bool值，回调函数中一般返回的是一个逻辑表达式，逻辑表达式的值对应布尔值

```html


```


## 过滤方法filter(filter)
- 可以对jQuery对象进行筛选，只留下被filter过滤后剩下的元素
