# re模块与正则表达式
[点击查看我整理的正则表达式和re模块的思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/re.png?raw=true)

[正则表达式工具](http://tool.chinaz.com/regex/)

## 正则表达式

> 对于确定的，不需要变化范围的字符，直接用相同的字符就可以匹配上。
> 正则表达式考虑的就是在同一个字符位置上可以匹配上的字符的变化范围。

- 字符组[]
	- 同**一个位置**上可能出现的这些字符就构成了一个字符组。包含在 [ ] 中
	- 常见字符组
		- 一个位置上数字变化
			- [0123456789]  <==> [0-9]
			- [123456] <==> [1-6]
		- 一个位置上字母变化
			- [a-z]小写
			- [A-Z]大写
		- 三个（或多个）位置上
			- [0-9][a-z][A-Z]
- 字符
	- 除了常见的数字和大小写字母之外，配合转义字符可以有更多的表达方式匹配更多的字符
		- .	除了换行符之外的任意字符，windows中换行符是\r\n，linux中是\n		
		- \w  字母、数字、下划线  
		- \W  除了\w以外的任何字符（包括汉字）
		- \d   单个数字
		- \A	仅仅匹配字符串开头
		- \b	仅仅匹配单词的开头和结尾（不是字符串的开头和结尾）
			- python中的单词：由Unicode字母或下划线组成的序列。因此单词的结尾就是由空格、非字母数字、非下划线的Unicode表示
		- \B	匹配一个单词不再开头或结尾的字符。
		- \D   非单个数字
		- \s    单个空格
		- \S	非单个空格
		- \t    单个制表符
		- \n	单个换行符
		- a|b	字符a或字符b
		- ^ 	字符串的开始，只从字符串开头匹配指定字符
		-$ 	字符串的结尾， 只从字符串结尾匹配指定字符，或者是上一个换行符结尾的字符串，在多行模式下，匹配换行符之前的字符串
		- \ 	消除上述特殊字符含义，常用的比如  \(   \)  
		- ()	匹配括号中的表达式，主要用于量词
		- []	字符组
		- [^ ...]	匹配一个除了字符组中字符以外的所有字符




```


```



- 量词（作用于前面被修饰字符）
	- *	重复0次或多次	{0, }
	- +	重复1次或多次	{1, }
	- ？	重复0次或1次		{0, 1}
	- {n}	重复n次			{n}
	- {n,}重复n次或更多次	{n, }
	- {n,m}重复n到m次	{n, m}

- 贪婪匹配
	- 对于  *(0次或多次)  +(1次或多次)   ?(0次或1次)，在满足匹配的情况下，总是匹配尽可能长的字符串。
	- 举个例子

```
用  <.*>	匹配    <a>b<c> 
结果:	<a>b<c>	而不会是 <a>
非贪婪模式：<.*?>
匹配结果： 	<a>

```



- 非贪婪匹配模式
	- 在贪婪匹配模式的*  +  ?  后面加上一个问号？就可关闭贪婪模式

-  '  .*?x'
	-  前面取任意长度的字符，直到一个x出现

- 转义字符
	- python中的正则表达式和被匹配内容都是字符串的形式出现。 
	- 正则：  '\\\\d'  --->    待匹配内容'\\d'
	- 应该使用原始字符串，让正则表达式中的\d不再具有特定含义。
		- r'\d' --->  待匹配内容      r'\d'
		- 即： r'\n' 标识两个字符 \ 和 n   ，而'\n'表示一个字符

- (?P<name>...)	分组命名
	- 这个组中  ...  匹配到的字符串可以通过组名称name进行访问
	- 组名称的定义方式必须遵守python标识符规定
	- 每个组名称在所在正则表达式中只能定义一次（组名必须唯一）
- 通过分组名对分组表达式进行调用
	- (?P=name)
	- 举个例子

```python
ret = re.search('<(?P<name>\w+)>\w+</(?P=name)>', '<h1>hello</h1>')

print(ret.group())

--------------------------------------------------------------

<h1>hello</h1>
```

- 正则表达式中的注释
	- (?# . . .) python会忽略括号中的内容

- (?<! ...)
- (?<= ...)


- 注意
	- 匹配字符串中的（）
		- 方法一： 	\( \)
		- 方法二：	[(][)]



## re模块
### re.Method
- 关于参数中的flags标志位参数。控制表达式的匹配方式，如是否区分大小写，多行匹配
- 常见的flags：
	- flag = DOTAALL  
		- 使`.`可以匹配任意字符，包括换行符 
	-flag = MULTILINE
		- 开启此模式，`$` 将能够匹配换行符之前的内容
		- `foo.$` 匹配  `foo1\nfoo2\n` 时，将匹配到`foo1` 和 `foo2`    

	 


- re.compile(pattern, flags=0)	将正则表达式编译成一个正则表达式对象。
	- Compile a regular expression pattern, returning a pattern object.
	- pattern： 正则表达式
	- flags：待补充？
	- 表达式在单个程序中多次使用的时，使用re.compile()可以保存生成的正则表达式对象，提高重用效率

```python
prog = re.compile(pattern)
result  = prog.match(string)  # string为被匹配字符串

--------------------------
# 等价于
result = re.match(pattern, string)
```

- re.search(pattern, string, flags=0)在字符串string中查找模式匹配，直到找到**第一个匹配**然后返回，如果字符串没有对应的匹配，就会返回None
	- 返回值为match  object
	- Scan through string looking for a match to the pattern , returning a match object . or None if no match was found 
	- pattern： 正则表达式
	- flags：待补充？
	- 返回值为 match object对象，通过该对象的group()方法来获取匹配的字符串，如果没有则返回None，返回的None调用group()方法会报错**（AttributeError， 使用Try...except分支处理）**
		- match对象总有一个True的bool值，在正则匹配时，可以用if语句类判断search()或match()是否返回了一个match对象

```python

str = 'my name is hahaha'
pattern = 'n.*?i'
ret = re.search(pattern, str)
print(ret)
print(ret.group())
-----------------------------------

<_sre.SRE_Match object; span=(3, 9), match='name i'>
name i
```

- re.match(pattern, string, flags=0) 和re.search()相似，不过**只在字符串的开头进行匹配**
	- Try to apply the pattern at the start of the string of the string , returning a match object, or None if no match was found 
	- pattern： 正则表达式
	- flags：待补充？
	- 返回值为 match object对象，通过该对象的group()方法来获取匹配的字符串，如果没有则返回None，返回的None调用group()方法会报错


```python
str = 'my name is hahaha'
pattern = 'm.*? '		#问号后面有一个空格昂
ret = re.match(pattern, str)
print(ret)
print(ret.group())
----------------------------------------
<_sre.SRE_Match object; span=(0, 3), match='my '>
my 
```

- re.fullmatch(pattern, string, flags=0) 如果**整个string**和pattern的正则表达式匹配，就会返回完整的match object对象，否则就返回None

```python
str = 'my name is hahaha'
pattern = 'm.*'
ret = re.fullmatch(pattern, str)
print(ret)
print(ret.group())
-----------------------------------------
<_sre.SRE_Match object; span=(0, 17), match='my name is hahaha'>
my name is hahaha

```


- re.split(pattern, string, maxsplit=0, flags=0)将字符串按照匹配到的子字符串拆分，并返回一个列表
	- maxsplit最大切割次数，默认0为最大切割次数，其余部分作为列表的最后一个元素返回
	- 注意：
		1. 正则表达式是组的时候，被匹配的对象不会被切割后舍弃，也会被保存在列表中
		2. 正则表达式不是组的时候，被匹配对象会在从strin切割后被舍弃，得到的是不包含匹配正则的子字符串的列表
		3. 简单来说：组匹配格式下，保留所匹配的项，非组匹配格式下，不保留匹配到的项


```python
>>> s = 'Ziawang,ziawang,ziawangs'
>>> import re
>>> re.split('\w+', s)      
['', ',', ',', '']
>>> re.split('\W+', s)
['Ziawang', 'ziawang', 'ziawangs']
>>> s = 'Ziawang,  ziawang,  ziawangs'
>>> re.split('\W+', s)
['Ziawang', 'ziawang', 'ziawangs']
>>> re.split('(\W+)', s)
['Ziawang', ',  ', 'ziawang', ',  ', 'ziawangs']
>>> re.split('\W+', s, 1)
['Ziawang', 'ziawang,  ziawangs']
-----------------------------------------------------------------------------------
>>> 
>>> s = '...ziawang, ziawang...'
>>> re.split('(\W+)', s)
['', '...', 'ziawang', ', ', 'ziawang', '...', '']
>>> re.split('x*', 'axbc')

Warning (from warnings module):
  File "D:\Python36\lib\re.py", line 212
    return _compile(pattern, flags).split(string, maxsplit)
FutureWarning: split() requires a non-empty pattern match.
['a', 'bc']
>>> 
```

- re.findall(pattern, string, flags=0)返回所有满足匹配条件的结果，放在列表中返回
	- string被从左至右扫描
	- 

```python
str = 'my name is hahaha'
pattern = 'm.*? '    #问号后面有一个空格昂
ret = re.findall(pattern, str)
print(ret)
---------------------------------------------------
['my ', 'me ']
```

- re.findall()优先级
- findall会把组匹配得到的结果优先返回，结果会少了部分想要的字符串
- 消除组匹配方式返回优先的权限，可以在组()内部开头加上  ?:   ==>     (?:)

```python

str = 'hello my name is ziawang'
ret = re.findall('my\s(n.*? )', str)
print(ret)
----------------------------------------
# 结果：
['name ']
```

取消优先级

```python
str = 'hello my name is ziawang'
ret = re.findall('my\s(?:n.*? )', str)
print(ret)
-------------------------------
['my name ']
```

- finditer(pattern, string, flags=0)返回一个存放匹配到的match object对象的迭代器

```python
str = 'my name is hahaha'
pattern = 'm.*? '
ret = re.finditer(pattern, str)
print(ret)
print(next(ret).group())
print(next(ret).group())
-----------------------------------------------------------
<callable_iterator object at 0x000002788823DB00>
my 
me

```


- re.sub(pattern, repl, string, count=0, flags=0) 将string中满足pattern正则的substring替换成repl
- re.subn(pattern, repl, string, count=0, flags=0) 也是用repl替换匹配到的字符串，但是会返回一个元组
	- (new_string, number_of_subs_made)  即替换的结果，替换的次数

```python
s = 'ziawang is 23 years old 23 years old'
print(re.sub('\d*?', '18', s, 1))
print(re.sub('\d*?', '18', s, 2))
print(re.subn('\d*?', '18', s, 1))
print(re.subn('\d*?', '18', s, 2))
-----------------------------------------------------
18ziawang is 23 years old 23 years old
18z18iawang is 23 years old 23 years old
('18ziawang is 23 years old 23 years old', 1)
('18z18iawang is 23 years old 23 years old', 2)
```



## 正则表达式对象regex
- 即经过上面re.compile()编译得到的对象
- regex.Method
	- regex.match(string, [start[end]])		返回对应的match object
	- regex.search(string, [start[end]])		返回对应的match object
	- regex.fullmatch(string, [start[end]])		返回对应的match object
	- regex.split(strinf,[start[end]])
	- regex.findall(string[, pos[, endpos]])
	- regex.finditer(string[, pos[, endpos]])
	- regex.sub(repl, string, count=0)
	- regex.subn(repl, string, count=0)
	- regex.flags
	- regex.groups
	- regex.pattern
	- regex.groupindex


## match object
- match对象总有一个True的bool值，在正则匹配时，可以用if语句类判断search()或match()是否返回了一个match对象（这样可以避免匹配到错误后使用group方法而报错）

```python
match = re.search(pattern, string, flags=0)
if match :
 	process(match)
```

- match_attribute
	- 
	- 
## match_Method
- match.group([group1, . . . ]) 返回match对象的一个或多个子组
	- 如果只有一个参数，就返回单个字符串
	- 如果是多个参数，就返回一个元组
	- 如果没有参数, 返回整个匹配到的字符串
	- 组号不在pattern定义的组数范围内，IndexError
	- 可以通过正数来获取group的结果
		- 0   以字符串返回整个匹配结果
		- 1   以字符串返回第一个匹配结果
		- 2  以字都穿返回第二个匹配结果
		- . . . . .

```python
>>> m = re.match(r"(\w+) (\w+)", "Isaac Newton, physicist")
>>> m.group()
'Isaac Newton'
>>> m.group(1)
'Isaac'
>>> m.group(2)
'Newton'
>>> m.group(1, 2)
('Isaac', 'Newton')
>>> 
```

- match.group()中也可以通过组名来获取该组匹配到的字符串

```python
>>> s = 'ziawang is 23 years old this year'
>>> match_object = re.match('(?P<name>z.*? )(\w+? )', s)
>>> match_object.group()
'ziawang is '
>>> match_object.group(0)
'ziawang is '
>>> match_object.group(1)
'ziawang '
>>> match_object.group(2)
'is '
>>> match_object.group('name')
'ziawang '
>>> 
>-------------------------------------------------------------------------------------------------------------------------------------------------
>>> ret = re.match('(?P<z>\w+)\s*(?P<l>\w+)', name)
>>> ret
<_sre.SRE_Match object; span=(0, 18), match='ziawang luxiaoqian'>
>>> ret.group()
'ziawang luxiaoqian'
>>> ret.group(1)
'ziawang'
>>> ret.group(2)
'luxiaoqian'
>>> ret.group(0)
'ziawang luxiaoqian'
>>> ret.group('z')
'ziawang'
>>> ret.group('l')
'luxiaoqian'
>>> ret.group('z', 'l')
('ziawang', 'luxiaoqian')
>>> ret.group(1, 2)
('ziawang', 'luxiaoqian')
>>> 
```

- match.groups(default = None) 返回一个元组，存放了所有组匹配到的字符串
	- 在利用组对string匹配的时候，有的组并不会匹配到值，通过default可以为这些没匹配到的对象设置默认值，python默认为None


```python
s = '24'
mat = re.match(r'(\d+)\.?(\d*)?', s)
print(mat.groups())
print(mat.groups(default='hahaha'))
----------------------------------------------------------
('24', '')
('24', '')			# 没有输出'hahaha'  难道我写错了   =  = ！

```

- match.groupdict(default = None) 返回组名和匹配到的字符串对应的字典，不包含没有别名的组
	- 

```python
s = '24'
mat = re.match(r'(?P<biubiubiu>\d+)\.?(?P<bangbangbang>\d*)?', s)
print(mat.groupdict())
print(mat.groupdict(default='hahaha'))
--------------------------------------------------------
# 这个默认值貌似也是改不了的  = =！
{'biubiubiu': '24', 'bangbangbang': ''}
{'biubiubiu': '24', 'bangbangbang': ''}

```

- match.span([group])
- match.pos





