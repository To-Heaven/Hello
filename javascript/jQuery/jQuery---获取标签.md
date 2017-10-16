# 获取标签
> 和CSS中选择器定义规则相同

- **如果没有匹配到任何元素，返回一个空数组，而不会报错**

## 选择器
- 格式`$('xx')`或`jQuery('xx')`
	- 基本选择器
	- id选择器		`$('#value_id')`
	- 类选择器		`$('.value_class')`
	- 标签选择器		`$('tag_name')`
- 组合选择器		
	- 多元素选择（并列）      `$('selector1, selector2')`
	- 后代选择				`$('par_selector sub_selector')`
	- 兄弟选择				`$('selector1~selector2')`
	- 子类选择器				`$('par_selector>child_selector')`
	- 毗邻选择(必须紧挨着)		`$('selector1+selector2')`
- 指定标签的类		`$('tag.value_cls')`
- 指定标签的id		`$('tag#value_id')`
- 属性选择器 
	- 只通过属性选择					`$('[attr]')`
	- 通过属性及值选择				`$('[attr=value]')`
	- 选择指定标签的属性值 			`$('tag[attr=value]')`
	- 指定标签的属性包含属性值			`$('tag[attr~=value]')`
	- 指定标签的属性包含以指定字符开头	`$('tag[attr^=val]')`
	- 指定标签的属性包含以指定字符结尾  `$('tag[attr$=lue]')`
	- 指定标签的属性包含指定字符		`$('tag[attr*=al]')` 
- 所有			`$('*')`
- 注意：
	- 匹配`class = 'red green'`并且保证不匹配到`class = 'red'`或`class='green'`的标签时应使用`$('.red.green')`而不是`$('.red, .green')`
	- 组合选择器（层级选择器）支持多层选择
		- `$(div.d p a)`匹配类为d的div标签下包含a标签的p标签

## 筛选器
- 分隔符为`:`
	- 按位置筛选
		- `:first`
		- `:last`
		- `:even`得到数组中索引为0或偶数元素的jQuery对象
		- `:odd` 得到数组中索引为奇数的元素的jQuery对象
		- `:eq(index)`数组中第num个元素的jQuery对象
		- `:gt(index)`大于index元素的jQuery对象
		- `:lt(index)`小于index元素的jQuery对象
		- `:not(selector)`
			- selector可以是上述任意选择器
	- 按内容筛选
		- `:empty`
		- `:focus`选取当前具有焦点的元素
		- `:target`选择由文档URI的格式化识别码表示的目标元素
		- `:contains(text)`包含text，根据文档内容查找
	- 按子节点筛选
		- `:has(selector)`包含selector匹配的标签
			- 注意引号对选择器的区分
		- `:parent`带有子元素并且包含文本的元素
	- 按属性
		- `:enabled`所有启用的元素
		- `:disabled`所有被禁用的元素
		- `:hidden`所有隐藏的元素
		- `:visible`所有可见的元素
		- `:header`所有标题（h*）元素
	- 表单
		- `:text`
		- `:input`
		- `:radio`
		- `:submit`
		- `:checkbox`
		- `:reset`
		- `:button`
		- `:file`
		- `:password`
		- `:image`
		- 表单元素状态
			- `:selected`
			- `:checked`


## 筛选方法
- `j.first()`
- `j.last()`
- `j.parent(filter)`返回被选元素的直接父元素
	- filter缩小搜索父元素范围的选择器表达式
- `j.hasClass(str_classname)`
- `j.eq(index)`




## 查找
- `j.children(filter)`
	- 返回匹配对象的直接子元素（只会沿着DOM树向下遍历一层级）

- `$(selector).find(filter)` 
	- 返回匹配后代的所有满足filter的元素

- `$(selector).next(filter)` 
	- 返回匹配元素之后的第一个同级元素
	- 使用filter的时候，会在同级别元素中进行匹配，直到遇到一个满足filter条件的元素才返回该元素

- `$(selector).nextAll(filter)`
	- 返回匹配元素之后的所有同级别元素
	
-`$(selector).nextUntil(filter_stop)`
	- 返回selector匹配到的元素与filter_stop之间的所有同级别元素
	

- `$(selector).parent(filter)`
	- 返回匹配元素的直接父元素 
	- 使用filter时，会一直向上一层层匹配满足filter的元素，知道某一个祖先元素满足

- `$(selector).parents(filter)`
	- 返回匹配元素的父元素和所有祖先元素

- `$(selector).parentUntil(stop, filter)`
	- 

- `$(selector).prev(filter)`
	- 返回匹配元素前面第一个同级别元素
	- 使用filter时，会在同级别元素中向前匹配，直到匹配到第一个满足filter的元素并返回

- `$(selector).prevAll(filter)`
	- 返回匹配元素前面的所有元素

- `$(selector).prevUntil(stop_filter)`
	- 返回selector匹配到的元素与top_filter匹配到的元素之间的所有元素

- `$(seletctor).siblings(filter)`
	- 返回匹配到元素的所有同级别元素




## 遍历转换方法map()
> 其实和数组的map()方法用法相同

- `$(selector).map(func)`

