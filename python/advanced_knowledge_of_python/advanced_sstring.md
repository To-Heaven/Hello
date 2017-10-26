## 字符串进阶

- **在python中，遇到未闭合的括号时，python会将多行代码视为一行**(pythonic)

- python不同版本中字符串的区别
	- python2中字符串有两种，`bytes(str)`和`unicode`，使用引号创建的字符串默认为`bytes`类型
		- 创建Unicode字符串可以在引号前加上前缀u，`u'this is a string of unicode'`
	- python2中字符串也有两种，`unicode(str)`和`bytes`，使用引号创建的字符串默认为`unicode`类型
		- 创建bytes字符串可以在尹浩仟加上前缀b，`b'this is a string of bytes'`
	- 字符串类型判断要注意
		- 在python2中
			- 判断一个字符串是不是bytes类型字符串有两种方法
				- `isinstance(s, bytes)`
				- `isinstance(s, str)`
			- 判断一个字符串是不是Unicode类型
				- `isinstance(s, unicode)`
			- 判断一个对象是不是字符串
				-    `isinstance(s, basestring)`
		- 在python3中（默认引号创建的就是Unicode）
			- 判断一个字符串是不是Unicode类型字符串只有一种
				- `isinstance(s, str)`
			- 判断一个字符串是不是bytes类型
				- `isinstance(s, bytes)`
			- python3中泵使用`isinstance(s, basestring)`判断，其实使用`isisntance(s, str)`即可 
- `split([sep[,maxsplit]])`的两种算法
	- 当不指定参数的时候，split会先去除两边的空格，然后将字符串中的多个空格视为一个
	- 当指定参数为`' '`空格的时候，split会严格按照sep参数来对字符串进行切割

```python
>>> s = ' haha xixi   dudu '
>>> s.split()
['haha', 'xixi', 'dudu']
>>> s.split(' ')
['', 'haha', 'xixi', '', '', 'dudu', '']
>>>
>>> ''.split()
[]
>>> ''.split(' ')
['']
>>>
```


- startswith和endswith的参数
	- 当两者的第一个参数为子字符串的时候，python会在字符串中查找相同的子字符串
	- 当两者的第一个参数为元组的时候，python匹配元组中的字符串元素，并返回匹配的结果

- 判断子字符串是否存在于字符串中的时候，使用`in`关键字代替find/index
   

