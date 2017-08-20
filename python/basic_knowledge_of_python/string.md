## 字符串
- 定义字符串
	- name = 'ziawang'    --->   name = str('ziawang')
		- 引号可以是单引号，双引号，三引号
	- type(name)
- 原始字符串  r  (raw)
	-  r' prefix that disables most escape sequence processing'

```python
>>> print('asdasdas\tdasd')
asdasdas	dasd
>>> print(r'asdasdas\tdasd')
asdasdas\tdasd
>>> 
```


## 字符串的获取
- using the str constructor
- 字符串的切片
	- 过大的索引值（下标值大于实际长度）会被字符串实际长度代替
	- 当从下边界指向上边界方向与步长方向相反的时候，就会返回空字符串

```python
>>> a = 'qwertyu'
>>> a[0:200]
'qwertyu'
>>> a[200:6]
''
>>> a[-1:-2]
''
>>> a[-5:0]
''
>>> a[-5:-1]
'erty'
>>> a [0:]
'qwertyu'
>>> a [:0]
''
>>> a[:-1]
'qwerty'
>>> a[0] == a[0:1]
True
>>> 
```

- **字符串不可以被更改，因为他们是不可变的**


```python
>>> b = 'qa'
>>> b[0] = 'a'
Traceback (most recent call last):
  File "<pyshell#18>", line 1, in <module>
    b[0] = 'a'
TypeError: 'str' object does not support item assignment
>>> 
```


## 字符串操作
- 字符串的连接方式
	- 一个字符串是一个整体，它是不可变的类型
	- 可以用  +  操作符连接（粘到一起，中间不会有空格）
	- 可以用逗号隔开，连接后会在中间存在空格
	- 如果没有加号，相邻的两个字符串会自动的连接在一起，但是这种情况不适用于一个字符串加上一个指向字符串的变量。这种情况下只能使用加号来连接代表字符串的变量和字符串


``` python
>>> str1 = 'ziawang'
>>> str2 = 'haha'
>>> str1+str2
'ziawanghaha'
>>> 'ziawang', 'haha'
('ziawang', 'haha')
>>> print('ziawang','haha')
ziawang haha
>>> 'ziawang''haha'
'ziawanghaha'
>>> 
```

- 可以用 *   来重复

``` python 
>>> 3*'ziawang' + ',haha'
ziawangziawangziawang,haha
```

- 字符串也可以用索引来获取组成字符串中的元素
- 以str = 'abcde'为例

```python
>>> s = 'abcde'
>>> s[-1]
'e'
>>> s[-5]
'a'
>>> s[-0]
'a'
>>> s[0]
'a'
>>> 
```

## 字符串的比较
- 字符串的比较是按照字符位置一次比较。   'A'<'Z'<'a'<'z'

```python
>>> s1 = 'a'
>>> s2 = 'asdfff'
>>> s1 == s2
False
>>> s1 > s2
False
>>> s1<s2
True
>>> s1 = 'abc'
>>> s2 = 'ab'
>>> s1 > s2
True
>>> 
```

## class str(object='')
## class str(object=b'', encoding='utf-8', errors = 'strict')
-  return a string of object
	-  if the object is empty, return a empty string
- arguments:
	- if neither encoding nor errors is given, str(object) return object.__str__()
	- if this object does not has the __str__method, str() fall back to returning repr(object)
	- Note: 
		- if at least  encoding or errors is given, object must be a byte-like object(bytes or bytearray）
		- if  object is a byte-like object , str(object, encoding, errors)   <==> bytes.decode(encoding, errors)

```python

>>> str()
''
>>> str(b'ziawang', encoding='utf-8')
'ziawang'
>>> str(b'ziawang')
"b'ziawang'"
>>> b'ziawang'
b'ziawang'
>>> 
```


## String Methods

- str.splitlines(keepends=False)
	- return a list of the lines in the string , breaking at the boundaries . line breaks are not included in the resulting list unless keepends is given and True
	
```python
>>> s = '012301230123'
>>> s = '12\n3\n\n456\r\n789\f4567\v'
>>> s
'12\n3\n\n456\r\n789\x0c4567\x0b'
>>> s.splitlines()
['12', '3', '', '456', '789', '4567']
>>> s.splitlines(keepends = True)
['12\n', '3\n', '\n', '456\r\n', '789\x0c', '4567\x0b']
>>> 

``` 

- str.capitalize()返回一个字符串，首字母大写，其他的字母小写
> **原str并未被修改**

    Return a capitalized version of S, i.e. make the first character
    have upper case and the rest lower case.

```python
>>> str  = 'ziawang'
>>> str.capitalize
<built-in method capitalize of str object at 0x00000155690D4DF8>
>>> str.capitalize()
'Ziawang'
```

- str.title()  返回一个标题化的字符串，所有的单词以大写开始，其余字母均变成小写

```python
>>> s = 'ziawang   is  23 years old'
>>> s.title()
'Ziawang Is 23 Years Old '
>>> 
```

-  str.casefold()返回一个字符串，全部小写（支持多种编码，lower只支持ASCII编码，即支队A—Z有效）
> **原str并未被修改**

```python
>>> str.casefold()
'ziawang'
>>> str
'ZiaWang'
>>> 
```

- str.swapcase()反转字符串中字母的大小写
	Return a copy of S with uppercase characters converted to lowercase
	and vice versa.


- str.center(width[fillchar])返回一个指定width长度的字符串，并将原字符放在中间，其他位置用fillchar填充

```python
>>> str = 'ZiaWang'
>>> str.center(20, '-')
'------ZiaWang-------'
>>> str
'ZiaWang'
>>> 
```

- str.count(sub[start[end]])返回字符串中某字符串出现的次数，可以用start和end来限制统计范围

```python
>>> str = 'qwertqwerqwer'
>>> len(str)
13
>>> str.count('q')
3
>>> str.count('q',1)
2
>>> str.count('q',1,8)
1
>>> 
```
- str.endswith(sub[start[end]])判断字符串的结束位置的字符是否是指定值，也可以用start和end限定判断范围

```python
>>> str = 'qwertqwerqwer'
>>> len(str)
13
>>> str.endswith('e',0,12)
True
```

- str.expandtabs(tabsize=8) 返回一个字符串的副本，并选择指定的tabsize对字符串进行扩展
- 当tabsize<len(str)的时候，tabsize为从\t位置开始将要空出的空格数
- 当len(str)<tabsize的时候，从\t位置要空出的空格数为tabsize-len(str)

```python
>>> str = '1234\t1234\t1234'
>>> str
'1234\t1234\t1234'
>>> print(str)
1234	1234	1234
>>> str.expandtabs(10)
'1234      1234      1234'
>>> str.expandtabs(0)
'123412341234'
>>> str.expandtabs(2)
'1234  1234  1234'
>>> str.expandtabs(7)
'1234   1234   1234'
>>> str = '12345'
>>> str.expandtabs(0)
'12345'
>>> str = '12345\t12345\t12345'
>>> str.expandtabs(0)
'123451234512345'
>>> str.expandtabs(5)
'12345     12345     12345'
>>> str.expandtabs(9)
'12345    12345    12345'
>>> str.expandtabs(11)
'12345      12345      12345'
>>> 
``` 

- str.find(sub[start[end]]) 从字符串的左边开始找
- Return the lowest index in the string where substring sub is found within the slice  s[start:end].Optional arguments start and end are inter preted as in slice notation. **Return -1 if sub is not found.**

- str.rfind(sub[start[end]])  从字符串的右边开始找
- **注意**
	- 两种方法得到的结果是相同的，即该字符sub所在的索引是固定不变的

```python
>>> str = 'asdwqfcqfrwef'
>>> str.find('f')
5
>>> len(str)
13
>>> str.find('f',6)
8
>>> str.find('p')                   #返回 -1
-1
>>> str.rfind('c')
6
>>> str.rfind('c', -4, -6)
-1
>>> 
```

- str.join(iterable) 将序列sequence中的元素用str连接起来，返回一个生成新的字符串。但是要注意的是，该序列的元素应该是字符串，如果是数字就会报错

> 官方文档
> join(...)
> S.join(iterable) -> str
> Return a string which is the concatenation of the strings in the  iterable.  The separator between elements is S.

```python
>>> str = '-'
>>> phone_number = (156,9501,8792)
>>> str.join(phone_number)
Traceback (most recent call last):
  File "<pyshell#4>", line 1, in <module>
    str.join(phone_number)
TypeError: sequence item 0: expected str instance, int found
>>> p = ('156','9501','8792')
>>> str.join(p)
'156-9501-8792'
>>> 
```

- str.join()的问题在于它仅仅适用于字符串。这意味着通常需要一些转换

```python
>>> row = ['haha', 1, 2 ,3 , 4]
>>> row = ['haha', 1, 2, 3, 4]
>>> ', '.join(row)
Traceback (most recent call last):
  File "<pyshell#2>", line 1, in <module>
    ', '.join(row)
TypeError: sequence item 1: expected str instance, int found
>>> ', '.join(str(i) for i in row)            #最终还是要处理成字符串
'haha, 1, 2, 3, 4'
>>> 
```

- **其实根本不用这么麻烦，完全可以用print函数这样写**

```python
>>> row = ['haha', 1, 2, 3, 4]
>>> print(*row, sep=', ')
haha, 1, 2, 3, 4
>>> 


```
- str.format(*args, **kwargs) 格式化字符串，并返回格式化处理后的字符串。
- 在定制类时的操作

```python
class Demo:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return 'Demo({self.x}, {self.y})'.format(self=self)


d = Demo(1, 2)
print(d)\

------------------------------------------------------------

Demo(1, 2)

```


- 常用操作

```python
args = ['ziawang', 23, 'Beijing', 'stock and python']

s1 = "my name is {}, I'm {} years old this year, I'm living in {} now. I love {}".format(*args)
print(s1)
# 通过列表索引来设置参数
s2 = "my name is {0[0]}, I'm {0[1]} years old this year, I'm living in {0[2]} now. I love {0[3]}".format(args)
print(s2)
# 可以重复使用且位置任意指定
s3 = "I'm {0} ,I love {3}, I'm {0}".format(*args)
print(s3)
kwargs = {'name':'ziawang', 'age':23, 'city':'Beijing', 'favorite':'stock , python'}
# name、age等key在传入的时候是不需要用双引号的。
s4 = "my name is {name}, I'm {age} years old this year, I'm living in {city} now. I love {favorite}".format(**kwargs)
print(s4)
```

- format与格式化占位符区别

```python

# name = input('name：')
# age = input('age: ')
# hobby = input('hobby: ')
# print('''\
# ------%s  info----------
#     name : %s
#     age : %s
#     hobby : %s
# ------------------------
# '''%(name, name, age, hobby))

#  但是如果hobby有好几个呢？



name = input('name : ')
age = input('age :')
hobby = []
times = int(input('你有几个爱好？'))
while True:
    if times == 0:
        break
    hobby_l = input('请输入你的爱好： ')
    hobby.append(hobby_l)
    times -= 1
info = dict([('name', name),('age', age), ('hobby', hobby)])

s = '''
-------------------------
name : {name}
age : {age}
hobby : {hobby}
-------------------------
'''.format(**info)
print(s)
```

> 输出结果

```python
my name is ziawang, I'm 23 years old this year, I'm living in Beijing now. I love stock and python
my name is ziawang, I'm 23 years old this year, I'm living in Beijing now. I love stock and python
I'm ziawang ,I love stock and python, I'm ziawang
my name is ziawang, I'm 23 years old this year, I'm living in Beijing now. I love stock , python
```

- format 可以实现大部分字符串方法
	- {:>width}右边显示字符串
	- {:<width}左边显示字符串
	- {:^width}中间显示字符串
	- {:filed_char^width} 中间显示字符串，并且用fillled_char填充

```python
>>> '{:^30}'.format('centered')
'           centered           '
>>> '{:<30}'.format('left')
'left                          '
>>> '{:>30}'.format('right')
'                         right'
>>> '{:^30}'.format('center')
'            center            '
>>> '{:-^30}'.format('center_filled')
'--------center_filled---------'
>>> 

```

- format格式化输出时间
	- Note: {: formatter}前面要有冒号

```python
>>> format_time = datetime.datetime(2017, 8, 18, 20, 5, 20)
>>> 'format_time     ==》  {:%Y-%m-%d  %H:%M:%S}'.format(format_time)
'format_time     ==》  2017-08-18  20:05:20'
>>> 

```





- str.index(sub[start[end]])  和find一样，但是当没有找到substring的时候会爆出ValueError错误
- str.rindex(sub[start[]end])  从右边开始，也会爆出Value错误
##  用于检测字符串的方法
- str.isdigit()  检测字符串是否是只由数字组成。是则返回True
- str.isnumeric()检测字符串是否只由数字组成，但是只针对Unicode对象
- str.isalpha()检测字符串是否是只由字母组成
- str.isalnum()检测字符串是否是由字母和数字组成
- str.islower()检测字符串是否由小写字母组成
- str.isspace()检测字符串是否只由空格组成
- str.istitle() 检测字符串中所有单词的首字母是否全部都大写
- str.upper()检测字符串中所有字母是否全部都是大写


----------

- str.lower()返回一个全是小写字母的字符串，源字符串未改变
- str.upper()返回一个全是大写字母的字符串，源字符串未改变

```python
>>> s = 'ASDASD'
>>> s.lower()
'asdasd'
>>> s
'ASDASD'
>>> s = s.lower()
>>> s
'asdasd'
>>> s.upper()
'ASDASD'
>>> s
'asdasd'
>>> 
```
- str.ljust(width[fillchar])  返回一个源字符串左对齐，并且使用指定字符（默认空格）填充至长度width的新字符串
- str.rjust(width[fillchar]) 返回一个源字符串右对齐，并且使用指定字符（默认空格）填充至长度为width的新字符串

```python
>>> str = '12345'
>>> s = str.ljust(10, '*')
>>> s
'12345*****'
>>> s = str.rjust(10, '+')
>>> s
'+++++12345'
>>> 

```
- str.lstrip([char]) 截掉字符串左边指定的字符或字符串中存在的字符（默认是空格, \n, \t）
- str.rstrip([char]) 截取掉字符串右边指定的字符，或字符串中存在的字符（默认是空格,\n, \t）
- str.strip([char]) 截掉字符串首尾指定的字符或字符串中存在的字符（默认是空格, \n, \t），

```python
>>> s = '   ahha  haha   '
>>> s_new = s.strip(' ')
>>> s_new
'ahha  haha'
>>> s_new = s.strip('a')
>>> s_new
'   ahha  haha   '
>>> s_new = s.lstrip(' ')
>>> s_new
'ahha  haha   '
>>> s_new = s.rstrip()
>>> s_new
'   ahha  haha'
>>> s
'   ahha  haha   '
>>> str = 'ziawang'
>>> str = '   zi  wang  haha a '
>>> str.strip('a')
'   zi  wang  haha a '
>>> str.strip('a ')
'zi  wang  hah'
>>> str.strip('a z')
'i  wang  hah'
>>> str.strip(' ahzi')
'wang'
>>> 
```
- **用处**
	- 用于对用户输入的信息进行格式化（去除无用的内容）

```python
name = input().strip(' ')   # 去除多余的空格
password  = input().strip(' ')
```

- str.maketrans(intab, outtab)  创建子字符隐射的转换表，进行字符转换，返回值是新的字符串，源字符串不会改变
- intab 字符串中要被替代的字符组成的字符串
- outtab对应intab中字符的字符串
- **注意：python2.x**
	- 使用str.maketrans()方法必须要先从模块string中调用maketrans方法
- str.translate(table)  根据参数table提供的表来转换字符串中的字符
- 翻译表table通过maketrans()方法转换而来

```python
>>> s = 'ziawang'
>>> s = 'haha'
>>> l = s.maketrans('ha', 'xi')
>>> l
{104: 120, 97: 105}
>>> s.translate(l)
'xixi'
>>> 
```


- max(str) 
- min(str)
-  返回字符串中最大/小的字母


- str.partition(string)  返回一个三元元组。该方法将str的副本进行分割，第一个元素是分隔符左边的子字符串，第二个为分隔符本身，第三个为分隔符右边的子字符串

```python
>>> str = 'ziawangisfrom Bozhou'
>>> str.partition('is')
('ziawang', 'is', 'from Bozhou')
>>> 
```

- str.replace(old, new[maxtimes])把字符串中的old字符串替换成new字符串，maxtimes为替换的最大次数

```python
>>> str = 'ziawangisfrom Bozhou'*3
>>> str
'ziawangisfrom Bozhouziawangisfrom Bozhouziawangisfrom Bozhou'
>>> s = str.replace('ziawang', 'wangzihao',2)
>>> s
'wangzihaoisfrom Bozhouwangzihaoisfrom Bozhouziawangisfrom Bozhou'
>>> 
>
```  

- string.split(s = '',  num = string.count(s))  通过分隔符  s 对字符串进行切片，参数num为切割的次数
- str默认为所有的空字符，包括空格，换行\n，制表符\t
- num分割次数


```python
>>> str  = 'haha \t haha \thaha \thaha'
>>> str.split(' ', 2)
['haha', '\t', 'haha \thaha \thaha']
>>> str.split()
['haha', 'haha', 'haha', 'haha']
>>> 
```

- 应用场景：获取字符串中指定的子字符串（可以对用户输入的字符串进行格式化，每隔一定长度插入一个特定字符，这样方便之后用split切分成列表）

```python
while True :
	cmd = input('>> : ').strip()
	if len(cmd) == 0   :    continue
	cmd_new = cmd.split()
	print('命令shi:%s, 命令的参数是%s'%(cmd_new[0], cmd_new[1]))	
```

- str.zfill(width)  返回指定长度的字符串，字符串右对齐，前面填充0

```python
>>> s = 'haha'
>>> s.zfill(10)
'000000haha'
>>> s
'haha'
>>> 

```
- str.isdigit()判断一个字符串里面是否全是由数字组成，在python3中，isdigit只能识别str，byte和unicode，无法进行字符串中中文数字和罗马数字的判断
- str.isdecimal()在python3中只可以对str和unicode进行识别，不能识别byte，中文数字和罗马数字
- str.isnumberic()可以判断str，unicode，中文，罗马数字
> bytes 没有 isdecimal(), isnumber()方法
**应该选择str.isdigit()来进行数值判断，因为它适用于大部分场景**

```python
AGE = 56
while True :
	age = input('please input the age :').strip()
	if len(age) == 0:   continue
	if age.isdigit() :
		...balabala...
```



----------
## python字符串连接的三种方法以及其效率、适用场景
- 方法一：直接用加号连接

```python
info  = 'ziawang'  +  'is  23  years old  '  +  'living  in  Beijing'
```

- 方法二：str.join(iterable)方法

```python
info  = ['ziawang',  'is  23  years old  ',  'living  in  Beijing']
new_info = ''.join(info) 


name = 'haha'
age = '23'
city = 'Beijing'
l = [('%s Info'%(name)).center(30, '*'), 'age:  '+age, 'city:  '+city, 'end'.center(30, '*')]
print('\n'.join(l))
```

- 方法三： 替换

```python
new_info = '%s%s%s'%('ziawang', 'is  23  years old  ', 'living  in  Beijing')

info = ['ziawang', 'is  23  years old  ', 'living  in  Beijing']
  #  new_info = '%s'*len(info)%(*info)  这句语法是错误的语法
```

-说明:
	1. 方法一：
		- 简单直接，当连续进行较多的字符串进行连接的时候效率会低于join()
		- 字符串是不可变类型，当用加号连接两个字符串的时候会生成一个新的字符串，生成新的字符串就需要重新申请内存，当连续相加的字符串太多的时候效率会差

	2. 方法二：对多个字符进行连接时候效率高，只会有一次内存的申请。而且对list的字符进行连接时候，这个方法必须是首选
	3. 方法三：要处理的字符串个数较多的时候  %s 不太方便，（此处留着等方法） ，




----------
##  将字符串中的字母转换成字符存储到列表中

- 下面是笨方法

```python
>>> s = '123456789'
>>> l = [j for i,j in dict(enumerate(s)).items()]
>>> l
['1', '2', '3', '4', '5', '6', '7', '8', '9']
>>> 
```
- 其实直接用list()函数对tr进行处理即可
 * list(iterable) -> new list initialized from iterable's items

```python
>>> s = 'ziawang'
>>> l = list(s)
>>> l
['z', 'i', 'a', 'w', 'a', 'n', 'g']
>>> 

```

> perform            执行
> template           模板
> substitution    替换
> dumplicate      复制
> placeholder     占位符 
> precedence      优先权


## 超实用的模板字符串Template string
- **template string support $-based substitutions**
	- $$ is an escape, it is replaced with a single $ 
	- $identifier  names a substitution palceholder matching a mapping key of 'identifier'
	- ${identifier} is equivalent to $identifier. 
		- required when vaild identifier characters follow the placeholder but are not the part of the placeholder, such as ' ${noun}ification'

- string.Template(template_string)
	- create a template object

- template_string.substitution(mapping, **kwargs)
	- perform the template substitution, return a new string.
	- mapping : 
		- dictionary-like object with keys that match the placeholders in the template
	- **Note:**
		- keywords can be provided, but the keywords must in the placeholders
		- while both mapping and keywords exists. placeholders from keywords take precedence

- template_string.safe_substitution(mapping, *kwargs)
	-   if placeholders are missing from mapping and kwds, instead of raising a KeyError exception, the original placeholder will appear in the resulting string intact

```python
>>> template_string = Template('$name has a $thing')
>>> template_string.substitute(name = 'ziawang', thing = 'book')
'ziawang has a book'
>>> d = {'name':'ziawang', 'thing':'book'}
>>> template_string.substitute(d)
'ziawang has a book'
>>> template_string.substitute(name = 'ziawang')
Traceback (most recent call last):
  File "<pyshell#16>", line 1, in <module>
    template_string.substitute(name = 'ziawang')
  File "D:\Python36\lib\string.py", line 126, in substitute
    return self.pattern.sub(convert, self.template)
  File "D:\Python36\lib\string.py", line 119, in convert
    return str(mapping[named])
KeyError: 'thing'
>>> template_string.safe_substitute(name = 'ziawang')
'ziawang has a $thing'
>>> 

```























