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
	- 使用`<link>`标签引入css规则，这种方式不需要再style标签中
	- `<link rel="stylesheet" href="mycss.css" type="text/css">`
	

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


- 匹配指定元素（标签的类）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div p.p1 {
            color: #efff3d;
        }
        p.p2 {
            color: #b80304;
        }

    </style>
</head>
<body>
    <div class="d1">
        <p class="p2">p2-p</p>
        <p class="p1">p1-p</p>
    </div>
    <p class="p1">p1-p</p>
    <p class="p2">p2-p</p>

</body>
</html>
```


## 属性选择器
- 匹配包含`attr`属性的所有标签 `[attr]`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        [class]{
            color: #b80304;
        }
    </style>
</head>
<body>
<div class="s">
    div
    <p>继承自div，字体样式会和div相同</p>
    <p class="p1">p1=p</p>
    <p class="p2">p2-p</p>
    <span class="s">s-span</span>
</div>
    <p>这个没有继承自div</p>
</body>
</html>

```

- 匹配包含`attr`属性的指定标签（元素element）`element[attr]`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p[class]{
            color: #b80304;
        }
    </style>
</head>
<body>
<div class="s">
    div
    <p>继承自div，字体样式会和div相同</p>
    <p class="p1">p1=p</p>
    <p class="p2">p2-p</p>
    <span class="s">s-span</span>
</div>
    <p>这个没有继承自div</p>
</body>
</html>
```


- 匹配属性为`attr`并且属性值为`value`的指定标签 `element[attr="value"]`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p[class="p1"]{
            color: #b80304;
        }
    </style>
</head>
<body>
<div class="s">
    div
    <p>继承自div，字体样式会和div相同</p>
    <p class="p1">p1=p</p>
    <p class="p2">p2-p</p>
    <span class="s">s-span</span>
</div>
    <p>这个没有继承自div</p>
</body>
</html>
```


- 匹配属性为`attr`，并且属性值为多个由空格分隔的值其中的一个
	- `element[attr~="value"]`


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p[class~="p1"]{
            color: #b80304;
        }
    </style>
</head>
<body>
<div class="s">
    div
    <p>继承自div，字体样式会和div相同</p>
    <p class="p1 p2 s">p1=p</p>
    <p class="p2">p2-p</p>
    <span class="s">s-span</span>
</div>
    <p>这个没有继承自div</p>
</body>
</html>
```



- 匹配属性为`attr` 并且属性值以指定值开头`element[attr^="val"]`

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p[class^="p"] {
            color: #b80304;
        }
    </style>
</head>
<body>
<div class="s">
    div
    <p>继承自div，字体样式会和div相同</p>
    <p class="p1 p2 s">p1-p</p>
    <p class="p2">p2-p</p>
    <span class="s">s-span</span>
</div>
    <p>这个没有继承自div</p>
</body>
</html>
```

- 匹配属性为`attr`并且属性值以指定值结尾`element[attr$="val"]`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p[class$="2"] {
            color: #b80304;
        }
    </style>
</head>
<body>
<div class="s">
    div
    <p>继承自div，字体样式会和div相同</p>
    <p class="p1 p2 s">p1-p</p>
    <p class="p2">p2-p</p>
    <span class="s">s-span</span>
</div>
    <p>这个没有继承自div</p>
</body>
</html>
```



- 匹配属性为`attr`并且属性值包含指定字符的每个元素`element[attr*=val]`
	- 注意
		1. `val` 必须是被匹配属性值的子字符串，且顺序不能有错
		2. `val` 中只要有至少一个字符不存在与属性值中，将无法匹配到该属性值

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p[class*="1p"]{
            color: #b80304;
        }

        div[class*="xa"]{
            color: #efff3d;
        }
        span[class*="xy"]{
            color: #ff39eb;
        }
    </style>
</head>
<body>
<div class="abc">
    div
    <p>继承自div，字体样式会和div相同</p>
    <p class="p1 p2 s">p1-p</p>
    <p class="p2">p2-p</p>
    <span class="xyz">s-span</span>
</div>
    <p>这个没有继承自div</p>
</body>
</html>
```


## anchor伪类选择器
> 伪类表示标签的不同状态

- 用于控制链接的显示效果
- 链接`<a>`的四种状态
	- `a:link {属性：属性值}`未接触时的状态
	- `a:hover {属性：属性值}` 鼠标放在链接上的状态
	- `a:active {属性：属性值}` 点击链接时的状态
	- `a:visited {属性：属性值}`访问完链接之后的状态

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        a:link{
            color: black;
        }
        a:hover{
            color: #b80304;
        }
        a:active{
            color: #efff3d;
        }
        a:visited{
            color: aqua;
        }
    </style>
</head>
<body>
<a href="http://www.ziawang.com">点击访问</a>
</body>
</html>
```

- 还可以通过伪类对页面其他标签进行渲染（不论是块级标签还是内联标签）
	-举例：  实现当将鼠标移动到一个`div`盒子父级区域时，使该`div`产生变化
	- 注意：
		- __通过类选择器匹配元素时，一定一定不要忘了`.`__
		- 此方法不能实现鼠标移动到同级别`div`区域时实现渲染效果，即只能通过父级操作子集，不能平级操作

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div.inner1{
            background-color: black;
            width: 200px;
            height: 200px;
        }
        div.inner2{
            background-color: #b80304;
            width: 200px;
            height: 200px;

        }
        div.outer{
            background-color: #efff3d;
            width: 100%;
        }
        .outer:hover .inner1{
            background-color: aqua;
        }

    </style>
</head>
<body>
<div class="outer">
    <div class="inner1"></div>
    <div class="inner2"></div>
</div>
</body>
</html>
```


## before/after伪类
- 用于在指定的元素（标签）前后插入内容，插入的内容虽然能够在页面中显示出来，但是该内容并不在html文件代码中，是**不能选定的**
- 注意
	- 该插入的内容会紧挨着指定的标签（即使是`块级标签:after`）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div:after{
            content: "before div";
            background-color: #b80304;
        }
        span:after{
            content: "after span";
            background-color: #efff3d;
            display: block;
        }
    </style>
</head>
<body>
    <div class="d">div</div>
    <span>span</span>
</body>
</html>

```

## 继承
- 继承的优先级最低，任何声明的规则都可以覆盖继承的样式
- 后代继承的属性是有限制的
	- 一般情况下，后代只能从父代中继承**父代的字体样式**
	- 对于`border, margin, padding, background` 等属性不会被继承

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            color: #b80304;
        }
    </style>
</head>
<body>
    <div>
        div
        <p>p</p>
        <span>span</span>
    </div>
</body>
</html>
```

## 选择器优先级
- 选择器的权重

```
内联样式表 <p style=""  . . .>     1000
id选择器	 #id {属性：属性值}    100
class选择器 .class {属性：属性值}  10
标签选择器  div{属性：属性值}   1
继承                                                          0    
```

- 上述权重在小范围内是有效的，比如当#id{} 的优先级计算的值小于 .class{}计算的值时，还是会以id的优先级为准
	- 有id的时候，不考虑class即使class嵌套很多层

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        #a{
            color: #b80304;
        }
        .a .b .c .d .e .f .g .h .i .h .j {
            color: #efff3d;
        }
    </style>
</head>
<body>
    <div class="a">
        <div class="b">
            <div class="c">
                <div class="d">
                    <div class="e">
                        <div class="f">
                            <div class="g">
                                <div class="h">
                                    <div class="i">
                                        <div class="h">
                                            <div class="j" id="a">asdasdas</div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>
</body>
</html>
```

- 强制某一属性优先级最高  `!important`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p {
            color: #efff3d!important;
        }
       #d {
            color: #b80304;
        }
    </style>
</head>
<body>
    <p class="c" id="d">pppp</p>
</body>
</html>

```


- 属性选择器优先级和class选择器优先级相同，所以它俩作用到同一个标签时要看加载的顺序，后出现的决定标签样式

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        [myattr] {
            color: #efff3d;
        }
        /*.c {*/
            /*color: #b80304;*/
        /*}*/
        /*#id {*/
            /*color:black ;*/
        /*}*/
        div {
            color: aqua;
        }

    </style>
</head>
<body>
    <div class="c" id="id" myattr="myattr">haha</div>
</body>
</html>
```

- 综上所述
	- 单一的(id)高于共用的(class)，有指定的用指定的，未指定就继承离它最近的