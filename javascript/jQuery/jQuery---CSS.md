# jQuery操作CSS
- 使用jQuery对象的css方法
	- `$(selector).css(are_name| arr_name | map_KeyValue | [, value])`

## 获取当前CSS
- 获取一个
	- `$(selector).css('color')`
- 获取多个
	- `$(selector).css(['color', 'border'])`
	

## 修改当前CSS
- 修改一个
	- `$(selector).css('color', 'red')` or `$(selector).css({'color', 'red'})`
	
- 修改多个
	- `$(selector).css({'color': 'red', 'breder':'1px solid red'})`
	



  

## 偏移
- `$(selector).offset([{'top':xxx, 'left':xxx}])`
	- 无参数时: 返回匹配元素离浏览器视口的偏移量（单位为像素）
	- 有参数时：
		- map： 为包含top和left键值对的集合对象，单位为像素，`xxx`值不需要加单位`px`



- `$(selector).position()`
	- 返回匹配元素相对已经定位的父标签的位置
	- 比较的时最近的那个做过定位的祖先标签



- `$(selector).scrollTop([num])`
	- 无参数时，返回匹配元素离滚动条顶部的偏移(单位为像素)
	- 有参数时，设置匹配元素离滚动条顶部的偏移
	- 应用 ： 滚动条

- `$(selector).scrollLeft([num])`
	- 无参数时，返回匹配元素离滚动条左侧的偏移
	- 有参数时，设置匹配元素离滚动条左侧的偏移

## 尺寸
- `$(selector).height(num)`
	- 无参数时，返回元素内容的高度
	- 有参数时，设置元素内容高度
- - `$(selector).width(num)`
	- 无参数时，返回元素内容的宽度
	- 有参数时，色画质元素内容的宽度
- - `$(selector).innerWidth()`
	- 无参数，只返回包含padding内边距的宽度，不包含border
- - `$(selector).innerHeight()`
	- 无参数，只返回包含padding内边距的高度，不包含border
- - `$(selector).outerHeight()`
	- 无参数，只返回包含padding内边距的高度，包含border
- `$(selector).outerWidth()`
	- 无参数，只返回包含padding内边距的高度，包含border