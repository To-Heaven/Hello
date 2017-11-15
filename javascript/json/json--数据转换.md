## 为什么不用eval()
- JavaScript中我们可以使用`eval()`方法将一个json对象反序列化成一个JavaScript中的对象或者表达式，并且会执行其中的表达式，如果json数据的来源是一个不可靠的网站，这样做是非常危险的。因此现在的主流浏览器内部的解释器都实现了`json.perse()`方法进行读取json数据，提供了较好的安全性，但是速度上要比使用原生的`eval()`方法慢一些



## JSON.parse(text[, reviver])
- 反序列化，将**json字符串**转换成json对象
- parameters
	- text: 包含有效json对象的字符串
	- reviver:可选。 为给一个成员调用此函数

```html


```


## 从服务端接受json数据



## 从服务端接受json数组



## JSON.stringfy(value[, replacerp[,space])
- 序列化，将JSON对象转换成json字符串
- parameters
	- value: 一个有效的json对象
	- replacer: 用于转换结果的函数或数组
		- 函数： 将每一个json内成员的键和值。stringfy会使用该函数的返回值作为原始值进行转换
		- 数组： 转换数组中具有键值的成员
	- space： 为文本添加缩进。可以是正整数、"\t"等

```html

```

## 不同语言间数据结构的转换
- python中
	- 列表会被序列化成数组
	- 元组被序列化成数组
	- True/False会被转换成true/false
	- python中的对象、函数等不能被序列化成json，但是pickle可以
	- python中的数据被json.dump成json字符串之后，里面的所有数据类型都被转换成了带双引号的字符串包含在json字符串中

