## 操作属性
- `jQ.Attr(str_attr[, value])`
	- 设置或者返回匹配元素的属性对应的值
	- 注意：
		- 如果匹配到多个元素，会返回第一个属性值

- `jQ.removeAttr(str_attr)`
	- 移除每一个匹配元素的指定属性
	
- `jQ.prop(str_attr [, value])`
	-  返回或修改一个属性
	- 注意：
		- 如果匹配到多个元素，会返回第一个属性值
	-  注意
		-  对于value值为Boolean类型的属性，prop返回的就是true/false
		-  使用`Attr()`方法返回的是属性的字符串值，比如`checked`，不是布尔值	

```html
$('input[type=checkbox]').prop('checked', False)
$('input[type=checkbox]').prop('disabled', True)
$('input[checkbox]').prop('checked')
```

- 应用
	- 将数据库中筛选的记录存放到表格中的一行行时，将每一行`tr`元素的属性值设置成数据库中记录的id值，对于表格中`<td></td>`标签中从1开始的排列顺序，只是为了便于观察表格而已

## 操作属性class
- `jQ.addClass(str_classname)`
	- 向jQ匹配到的元素添加指定的类名

- `jQ.removeClass(str_classname)`
	- 移除匹配元素中指定的类名

- `jQ.toggleClass(str_classname)`
	- 如果匹配到元素的class_name存在（不存在）就删除（添加）一个类。

## 操作属性value
- `jQ.val(value | function)`
	- 为匹配到input框元素设置value属性值
	- 通过function的返回值来设置匹配到input框元素的值
	- 对于input文本框
		- 获取的是输入的内容
	- 对于checkbox
		- 获取的时第一个元素的value值
	- 对于select
		- 单选，就是获取值
		- 多选，获取的时一个数组，修改的时候也得是数组
	- 如果是设置value值，那么会将数组中匹配的所有对象的值都设置 



## 操作标签内容
- `$(selector).html(str_content | function)`
	- 无参数：返回匹配元素的内容
	- str_content: 设置所有匹配元素的内容
	- function: 通过function的返回值来设置匹配到元素的内容

- `$(selector).text(str_text | function)`
	- 无参数： 返回所有匹配元素包含的文本内容组合起来的文本
	- 有str_text： 设置所有匹配元素的文本内容
	- function： 通过function的返回值来设置匹配到元素的文本内容

## 设置块级区域大小
- `$(selector).width([width])`
	- 当不指定参数时，返回当前标签的宽度
	- 指定参数时，会设置标签的宽度

- `$(selector).height([height])`
	- 当不指定参数时，返回当前标签的高度
	- 指定参数时，设置当前标签的高度