## JSON.parse(text[, reviver])
- 将字符串转换成json对象
- parameters
	- text: 包含有效json对象的字符串
	- reviver:可选。 为给一个成员调用此函数

```html


```


## 从服务端接受json数据



## 从服务端接受json数组



## JSON.stringfy(value[, replacerp[,space])
- 与JSON.parse()相反，JSON.stringfy()用来将JSON对象转换成json字符串
- parameters
	- value: 一个有效的json对象
	- replacer: 用于转换结果的函数或数组
		- 函数： 将每一个json内成员的键和值。stringfy会使用该函数的返回值作为原始值进行转换
		- 数组： 转换数组中具有键值的成员
	- space： 为文本添加缩进。可以是正整数、"\t"等

```html

```
