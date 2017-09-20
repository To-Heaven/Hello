# CSS
- CSS是什么鬼
	- CSS是层叠样式表
	- CSS为HTML标记语言提供了一种样式描述，定义了其中元素的显示方式。利用它可以实现修改一个小的样式更新与之相关的所有页面元素。

- CSS规则结构
	1. 选择器
		1. 选择器即目标标签
	2. 声明（一条或多条）
		1. 声明即一个属性与属性值的键值对

```css
选择器 {
	属性:属性值；
	属性:属性值；
	. . .
	属性:属性值
		
}

```

## CSS的四种引入方式
1. 在`<head></head>`标签中使用`<style></style>`标签引入

```html
<head>
    <style>
        p {
            color: #b80304;
        }
    </style>
</head>
<body>
	<p>p1</p>
	<p>p2</p>
	<p>p3</p>

	<div>
	    <p>div p4</p>
	</div>
</body>
```

2. 单独把选择器和声明存放在css文件中，通过引用的方式调用css
	- 使用`<link>`标签引入css规则
	- `<link rel="stylesheet" href="mycss.css">`
	

```css
# mycss.css
p {
    color: #b80304;
}
```



```html
<head>
    <link rel="stylesheet" href="mycss.css">
</head>
<body>
	<p>p1</p>
	<p>p2</p>
	<p>p3</p>

	<div>
	    <p>div p4</p>
	</div>
</body>
```

3. 也是把css规则存放在css文件中，使用`@import`导入引用
	- `@import`要放在`<style></style>`标签中

```css
# mycss.css
p {
    color: #b80304;
}
```

```html
<head>
    <style>
        @import "mycss.css";
    </style>
</head>
<body>
	<p>p1</p>
	<p>p2</p>
	<p>p3</p>

	<div>
	    <p>div p4</p>
	</div>
</body>

```


4. 在想要修饰标签的`style`属性中指定

```html
<head>
</head>
<body>
	<p style="color: #b80304; background-color: aqua">p1</p>
	<p>p2</p>
	<p>p3</p>

	<div>
	    <p>div p4</p>
	</div>
</body>
```


## 基本选择器
- 标签选择器  
	- 直接将想要渲染的标签作为选择器
	- `tag {color="red"}`

- id 选择器
	- 将标签id属性为指定值的标签作为选择器
	- `#id_value {color="red"}`

-  class选择器
	-  将标签class属性相同的标签作为选择器
	-  `.class_name {color="red"}`

- 通配选择器
	- 将渲染`<body>`标签下所有嵌套的标签


## 组合选择器
- 多元素选择器  `div, p {color="red"}`
	- 同时匹配所有div元素或者p元素，两者之间用逗号隔开

```html
<head>
    <style>
        .d, .c{
            color: #b80304;
        }
    </style>
</head>
<body>
    <div class="d">
        <p class="c" id="p1">p1</p>
        <p class="c" id="p2">p2</p>
    </div>
    <p class="c">div 同级 p</p>
    <p id="p">p</p>
</body>
```


- 后代元素选择器 `div p  {color="red"}`
	- 匹配所有属于div标签向下所有层级中的p元素
	

```html
<head>
    <style>
        .d p {
            color: #efff3d;
        }
    </style>
</head>
<body>
    <div class="d">
        <p class="c" id="p1">p1</p>
        <div>
            <p>内层p</p>
        </div>
        <p class="c" id="p2">p2</p>
    </div>
    <p class="c">div 同级 p</p>
    <p id="p">p</p>
</body>
```


- 子元素选择器  `div>p  {color="red"}` 
	- 只匹配div的子元素p，即只想下匹配一层中的p元素
	
```html
<head>
    <style>
        .d>p {
            color: #efff3d;
        }
    </style>
</head>
<body>
    <div class="d">
        <p class="c" id="p1">p1</p>
        <div>
            <p>内层p</p>
        </div>
        <p class="c" id="p2">p2</p>
    </div>
    <p class="c">div 同级 p</p>
    <p id="p">p</p>
</body>
```



- 毗邻元素选择器  `div+p {color="red"} `
	- 匹配紧随着div元素之后的同层级p元素
	- 注意：
		- 必须是紧邻着，中间不能有其他标签
	
```html
<head>
    <style>
        .d+p {
            color: #efff3d;
        }
    </style>
</head>
<body>
    <div class="d">
        <p class="c" id="p1">p1</p>
        <div>
            <p>内层p</p>
        </div>
        <p class="c" id="p2">p2</p>
    </div>
    <!--<d>d</d>-->
    <p class="c">div 同级 p</p>
    <p id="p">p</p>
</body>

```




- 普通兄弟选择器 `div~p {color="red"}`
	-    匹配div元素之后的所有p元素
	-    注意：
		-    只能是同级别，所以叫兄弟选择器

```html
<head>
    <style>
        .d~p {
            color: #efff3d;
        }
    </style>
</head>
<body>
    <div class="d">
        <p class="c" id="p1">p1</p>
        <div>
            <p>内层p</p>
        </div>
        <p class="c" id="p2">p2</p>
    </div>
    <!--<d>d</d>-->
    <p class="c">div 同级 p</p>			# 这个下层p元素不会被匹配
    <div>
        <p>div_p</p>
    </div>
    <p id="p">p</p>
</body>
```