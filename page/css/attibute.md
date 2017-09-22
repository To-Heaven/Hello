# CSS

- 应该按照功能和内容对盒子区域进行划分

# 渲染文本
- 字体类型`font-family`
- 字体样式`font-style`
	- 默认`font-style:normal`
	- 意大利斜体`p {fone-style:italic}`
- 颜色`color`
	- a链接标签本身具有默认颜色，继承不会改变其颜色（因为继承优先级最低），但是字体大小会继承。因此在修改a标签的颜色时，需要选择器定位到a标签上再修改
- 文本字体粗细
- 文本水平对齐方式`text-align`
	- left
	- right
	- center
	- justify
- 文本垂直居中使用`line-height`属性 
- 文本字体大小`font-size`
- 行高`line-height`
	- 行高=字体大小+上半行距+下半行距
- 文本的装饰`text-decoration`
	- none 默认值，标准的文本
	- overline 显示文本上的一条线
	- underline 显示文本的下一条线
	- line-through 显示穿过文本的一条线
- 首行缩进`text-indent:xpx`
- 字母间距`letter-spacing:xpx`
- 单词间距`word-spacing:xpx`
- text-transform: capitalize/uppercase/lowercase ; 文本转换，用于所有字句变成大写或小写字母，或每个单词的首字母大写

# 渲染垂直对齐
- `vertical-line`可以设置元素内容的垂直对齐方式，但是**只对行内元素有效，对块元素无效**
- 可以用来调节图片和文字的位置关系
- 基线即英文字母`x`的下边缘所在的直线

![基线](https://github.com/ZiaWang/Hello/blob/master/picture/baseline.png?raw=true)

- vertical-line:middle
	- 将元素的顶端与父元素中文本的中间对齐
- vertical-line:bottom
	-  把元素的顶端与行中最低的元素的顶端对齐。
-vertical-line:textbottom
	-   把元素的底端与父元素字体的底端对齐。
- vertical-line:baseline
	- 默认此选项值，元素会放在父类的基线上
- vertical-line:npx
	- 使用像素设置垂直对齐的位置 


# 渲染背景
- 设置背景色`background-color`
- 设置背景图片`background-image`
- 设置背景图片是否重复
	- `background-repeat:no-repeat/repeat`
- 设置背景图片的位置
	- `background-position: 水平 垂直`
	- 使用`center top left bottom right`
		- 属性值成对出现，如果至指定了一个，另一个将是`center`
	- 使用百分值设置，只指定一个另一个为50%
	- 使用像素设置 ，
- 给背景图片设置大小（最好用百分比设置）
	- ` background-size:n%`
- 简写形式
	- `background:#ffffff/url('1.png') no-repeat right top;`

# 渲染图片
- vertical-align 


# 渲染边框
- 边框宽度`border-width`
- 边框颜色`border-color`
- 左边框`border-left`
- 下边框`border-bottom` 同理还有上边框，右边框
- 边框曲线类型`border-style`
	- `border-style:dotted`  全部是实线
	- `border-style:dotted solid` 上/下 点状 ，左/右 实线
	- `border-style:dotted solid double` 上点状 ，左右实线，下双线
	- `border-style:dotted solid double dashed` 上点状，右实线，下双线，左虚线
- 单独设置各边框style
	- `border-left-style:dotted`

# 渲染列表
- 设置列表项标志的类型。`list-style-type`
	- squre
	- roman
	- circle
	- **none**
-  将图象设置为列表项标志。 `list-style-image `
-  设置列表中列表项标志的位置`list-style-position`
- 去除列表项的标志`list-style:none`  适用于排布
- 通过`padding`属性取消列表的左间隙

# display属性
- display:none
	-  将指定标签在HTML页面中的内容隐藏，被隐藏的内容在页面中不占任何空间，当触发显示该隐藏内容时，页面中才会显示
- block
	- 将内联标签设置为块级标签
- inline
	- 将块级标签设置为内联标签
- inline-block
	- 用来列表布局
	- 给块级标签设置一个内联的特性，但是内联的块级区域会有间隔（使用margin-left解决）
- **内联标签设置成浮动之后就可以对其长宽进行设置了，也可以在使用display把它变成块级标签**
	- 因为其浮动起来就不是正常文档流了，就可以调节长款了


```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
      * {
          margin: 0;
          padding: 0;
      }
        .c1{
            background-color: #b80304;
            width: 200px;
            height: 200px;
            display: inline-block;
        }
        .c2{
            background-color: #efff3d;
            width: 200px;
            height: 200px;
            display: inline-block;
           margin-left:-5px;
        }
        .c3{
            background-color: blanchedalmond;
            width: 200px;
            height: 200px;
            display: inline-block;
        }
    </style>
</head>
<body>
<div class="c1"></div>
<div class="c2"></div>
<div class="c3"></div>
</body>
</html>
```

> visibility:hidden可以隐藏某个元素，但隐藏的元素仍需占用与未隐藏之前一样的空间。也就是说，该元素虽然被隐藏了，但仍然会影响布局




# 渲染内边距——padding
- 填充属性，设置元素的四个内边距
	- 简写形式`padding`
	- `padding-top`
	- `padding-bottom`
	- `padding-left`
	- `padding-right`

```html
padding 的顺时针简写方式
padding = 50px 20px   上下  左右
padding = 50px 20px 10px    上  左右  下
padding = 50px 20px 10px 30px   上右下左
```


- 盒子模型
	- 整个盒子 = 内容content + 内边距（填充）padding + 边框border 
	- 完整的盒子模型 =  盒子 +  外边距（边界）margin 

![](https://github.com/ZiaWang/Hello/blob/master/picture/hezimoxing.png?raw=true)





# 渲染外边距——margin
- 设置元素的四个外边距
	- 简写形式`margin`
		- 简写形式参数对应的位置和padding一样也是顺时针排序
	- `margin-top`
	- `margin-bottom`
	- `margin-left`
	- `margin-right`
	
- 居中的问题  `margin: 0 auto`
	- 当被margin属性渲染的块级标签指定了float属性的时（除了none），那么margin居中效果将无法实现
	- 当该标签的float属性为none时（默认为none），margin的渲染效果才会实现

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .c{
            width: 100%;
            height: 800px;
            background-color: bisque;
            border-style: double;
            border-width: 5px;
        }
        .sub1{
            background-color: #efff3d;
            width: 200px;
            height: 200px;
            float: left;
        }
        .sub2{
            background-color: red;
            width: 200px;
            height: 200px;
            float: left;
            margin: auto;
        }
        .sub3{
            background-color: cornflowerblue;
            width: 200px;
            height: 200px;
            margin: 10px auto;
        }
    </style>
</head>
<body>
    <div class="c">
        <div class="sub1"></div>
        <div class="sub2"></div>
        <div class="sub3"></div>

    </div>
</body>
</html>
```



# 浮动float
[脱离文档流分析](http://www.cnblogs.com/shenfangfang/p/5278528.html)

- 文档流
	- 元素在排版布局的过程中会按照自左向右，自上向下的顺序进行流式排列
- 脱离文档流
	- 将元素从普通的布局排版中拿走，其他盒子在进行定位的时候，会将脱离文档流元素当作不存在而进行定位（无视脱离文档流元素）
- 非完全脱离文档流（部分无视）
	- 使用float属性脱离文档流的时候，其他盒子会无视这个元素，但是其他盒子中的文本依然会为这个元素让出位置，环绕在脱离文档流元素的周围 
- 完全脱离文档流
	- 对于`position:absolute`完全脱离文档流的元素，其他盒子与其他盒子内的文本都会无视它
- 浮动文档流（属于脱离文档流）
	- 如果下一个元素是也是浮动的，那么这个下一个浮动元素就会紧贴在这个元素右边
		- 如果一行放不下这两个元素，那么A元素会被挤到下一行
	- 如果下一个元素是正常文档流（标准流）的块级元素，那么这个正常文档流块级就会部分无视该元素
	- 如果下一个元素是正常文档流（标准流）的内联元素，那么这个内联元素不会无视浮动文档流，并且会按顺序排列（浮动的框之后的inline元素，会为这个框空出位置，然后按顺序排列。）
- 解决浮动文档流与标准流重叠的方法
	- 对标准流元素这只margin样式

# 浮动文档流与其父级样式
- 如果父级盒子没有指定高度，浮动文档流（脱离文档流）不会为其父级div盒子撑开一个高度

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
<style type="text/css">
         * {
             margin:0;padding:0;
         }
        .container{
            border:1px solid red;width:300px;
        }
        #box1{
            background-color:green;float:left;width:100px;height:100px;
        }
        #box2{
            background-color:deeppink; float:right;width:100px;height:100px; 
        }
         #box3{
             background-color:pink;height:40px;
         }
</style>
</head>
<body>

        <div class="container">
                <div id="box1">box1 向左浮动</div>
                <div id="box2">box2 向右浮动</div>
        </div>
        <div id="box3">box3</div>
</body>
</body>
</html>

```

- .container和box3的布局是上下结构，上图发现box3跑到了上面，与.container产生了重叠，但文本内容没有发生覆盖，只有div发生覆盖现象。这个原因是因为第一个大盒子里的子元素使用了浮动，脱离了文档流，导致.container没有被撑开。box3认为.container没有高度（未被撑开），因此跑上去了。


- 解决方法
	1. 给父级盒子container指定高度，但是这种方法会限制其包含的内容大小
	2. 使用clear属性清除浮动
		- 对box3使用`clear:both`属性
		- 对container使用`.clearfix{content:''; display:block; clear:both}`
	3.  为父级盒子container设置overflow属性`overflow:hidden`


# 去除浏览器默认margin
- HTML的body标签本身也是一个盒子，在默认情况下，   body距离html会有若干像素的margin，不同的浏览器设置的值时不同的，如果要去掉这些默认的margin，使用下面通配选择器

```html
* {
margin:0;
padding:0
}

```

# margin
- 遇到父级标签的兄弟标签或者在继承关系的标签中找到至少包含border padding  content其中一个属性的标签为margin参考的基准

# 隐藏溢出：overflow属性
- `overflow:visiable`默认值，会将溢出的部分显示出来
- `overflow:hidden`
	- 将超出盒子的内容裁切隐藏
- `overflow:scroll`
	-  为盒子创建滚动条
	
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .d1 {
            width: 200px;
            height: 100px;
            background-color: antiquewhite;
            overflow: hidden;
            /*overflow: scroll;*/
        }
    </style>
</head>
<body>
    <div class="d1">
        <h2>这是一个标题</h2>
        <h2>这是一个标题</h2>
        <h2>这是一个标题</h2>
        <h2>这是一个标题</h2>
    </div>
</body>
</html>
```


# 使用clear属性清除浮动
- 注意：
	- 清除浮动是指站在当前使用该属性的元素考虑下，清除相邻元素的浮动性的效果。这只是为了实现相邻元素没有浮动性时当前元素的排版效果，并不会相邻元素的浮动性有任何影响

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .a{
            background-color: #efff3d;
            width: 200px;
            height: 200px;
            float: left;
        }
        .b{
            background-color: #b80304;
            width: 200px;
            height: 200px;
            float: left;
            clear: both;
        }
        .c{
            background-color: aquamarine;
            width: 200px;
            height: 200px;
            float: left;
        }

    </style>
</head>
<body>
    <div class="a"></div>
    <div class="b"></div>
    <div class="c"></div>
</body>
</html>
```




# 定位position属性
- 默认值 static ，即无定位。
	- 设定了static属性值的元素不能当作其他元素绝对定位的参照物。
	- 设置标签对象的top right bottom left 等值是不起作用的

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        * {
            margin: 0;
        }
        .d1{
            width: 100px;
            height: 100px;
            position: static;
            background-color: #efff3d;
            top: 20px;
            left: 100px;
        }
    </style>
</head>
<body>
<div class="d1"></div>

</body>
</html>
``` 



- float 半脱离文档流
- position:fixed   完全脱离文档流，固定定位，以可视窗口为参照物
- position:relative  不脱离文档流，以自身原位置为参照物
- position:absolute  完全脱离文档流，以已经定位的父级为参照物
	- 所谓已经定位的父级，即是父级标签中的position属性有属性值
- 注意
	- float与position冲突 只能有一个