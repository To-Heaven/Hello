# 修改标签
## 替换标签
- par.replaceChild(new_sub, old_sub)
	- 通过父标签来替换目标标签

# 修改标签属性
- 设置属性
	- 使用`obj.attr_name = xxx`
	- 使用`obj.setAttrbute(name, value)`

- 获取属性/查询属性值
	- `obj.attr_name`
	- `obj.getAttribute(属性名)`

- 删除属性
	- `obj.removeAttribute(属性名)`

- **注意**，对于`input, select, textarea`标签来说，要操作标签中的内容，不能使用`obj.innertext`或`obj.innerHTML`或`obj.textContent`。应该使用`obj.value`



# 修改节点内容

## 修改标签内文本
- 使用节点对象的`innerHTML`属性、`innerText`属性以及`textContent`属性
- 注意
	- 使用上述属性修改节点对象的内容后，页面被加载并执行script标签中js代码之后，页面中节点标签内的所有内容都会被修改成新内容。即原来该标签的样式会被清除
#### innerHTML
- 即可以修改节点文本而且可以修改节点内的节点树

```html
<body>
<div id="d1">
    这是一个div标签
    <p class="p1" style="display: inline-block; background-color: bisque">这是div内的p标签</p>
</div>
<script>
    var d1 = document.querySelector('#d1');
    console.log(d1);
    d1.innerHTML = '<h1>新的内容</h1>'
</script>
```

#### innerText和textContent
- innerText
	- 会把节点对象下的所有文本内容（包括子节点的文本内容）提取出来，但是不会返回隐藏元素的文本
- textContent
	- 会把节点对象下的所有文本内容（包括子节点的文本内容）提取出来，包括隐藏元素的文本 



## 修改标签的css样式
- 使用节点对象的`style`属性就可以操作节点所有的css样式
	- js中style内的属性会以驼峰命名的标识符定义
		- 如css中的background-color属性就对应`style.backgroundColor`

```html

```

## 修改标签的类
- 查看标签类名
	- `obj.className`属性
- 获取类名组成的list
	- `obj.classList` 
- 为节点增加类
	- `obj.classlist.add(cls_name)`
- 移除节点类
	- `obj.classlist.remove(cls_name)`  
