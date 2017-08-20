## 字典

- 字典以关键字为索引
- 字典中出现了相同的key时候，python会记住最后一个key的键值对作为其元素
- **关键字可以是任意的不可变类型**, 即字典的key必须是可hash类型
	- 比如元组，字符串，数值
	- **注意**
		- 当将变量作为键来进行创建字典的时候，这个变量必须指向一个不可变的对象。
	- **元组可以作为关键字是有条件的**
		- 元组中的元素必须也是不可变类型，如果它直接或间接的包含了可变对象，就不能当作关键字使用
	- **PS： 即使是将可变对象多重嵌套到tuple中，该tuple仍然不可以作为字典的关键字使用**
	- **字典可以看作无序的键值对组合**

```python
>>> d = {'name':'wangzihao', 'name':'ziawang', 'age': 23, 'skill':['python', 'stock']}
>>> d
{'name': 'ziawang', 'age': 23, 'skill': ['python', 'stock']}
>>> 
>>> ziawang = (23, 'Beijing')
>>> dict_ziawang = {ziawang : 'information of ziawang'}
>>> dict_ziawang
{(23, 'Beijing'): 'information of ziawang'}
>>> ziawang_changeable = ('ziawang', [23, 'beijing'])
>>> dic = {ziawang_changeable : 'information of ziawang'}
Traceback (most recent call last):
  File "<pyshell#138>", line 1, in <module>
    dic = {ziawang_changeable : 'information of ziawang'}
TypeError: unhashable type: 'list'
>>> a = (([1,2,3], 'a'), 'a')
>>> d = {a : '多重嵌套可变类型的tuple也不可以作为字典的关键字'}
Traceback (most recent call last):
  File "<pyshell#140>", line 1, in <module>                                                    #  报错！！！！
    d = {a : '多重嵌套可变类型的tuple也不可以作为字典的关键字'}
TypeError: unhashable type: 'list'
```


## 创建/生成字典的几种方法

1. 直接利用key-value对和大括号创建。（一个空的大括号将创建一个空字典）
2. 使用dict()构造函数直接从   key - value对中创建字典

> PS: 如果对应的序列中的元素个数不满足key-value的两个元素的条件，就会报出ValueError错误

```python
>>> tup = (('name', 'ziawang'), ('age', 23), ('skills', ['stock', 'python']))
>>> tup_dict = dict(tup)
>>> tup_dict
{'name': 'ziawang', 'age': 23, 'skills': ['stock', 'python']}
>>> tup = (('name', 'ziawang', '王子豪'), ('age', 23), ('skills', ['stock', 'python']))
>>> dict(tup)                                                                        #  ValueError错误
Traceback (most recent call last):
  File "<pyshell#17>", line 1, in <module>
    dict(tup)
ValueError: dictionary update sequence element #0 has length 3; 2 is required
>>> 

```



##  删除字典的元素和清空字典

```python
>>> dict = {'name':'ziawang', 'age':23, 'skills':['python', 'stock']}
>>> del dict[age]
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    del dict[age]
NameError: name 'age' is not defined
>>> del dict['age']
>>> dict
{'name': 'ziawang', 'skills': ['python', 'stock']}
>>> dict.clear()
>>> dict
{}
>>> del dict
>>> 
```

PS：还可以结合zip(a ,   b)函数来创建字典

```python
>>> key = ('name', 'age', 'skills')
>>> value = ['ziawang', 23, ('stock', 'python')]
>>> dict(zip(key, value))
{'name': 'ziawang', 'age': 23, 'skills': ('stock', 'python')}
>>> 
```
3. 直接利用dict(**kw)中的关键字参数，来创建字典

> 关键字参数可以看作是对一个变量赋值，而这个变量的命名就得遵照变量命名的规则。（例如不能以引号等开头，这就意味着变量名不可能是一个字符串，也不能是一个数字或数字开头）

```python
>>> dict('name'='ziawang', 'age'=23, 'skills'=['stock', 'python'])
SyntaxError: keyword can't be an expression                               #错误的写法
>>> dict(1='ziawang', age=23, skills =['stock', 'python'])
SyntaxError: keyword can't be an expression                            #错误的写法
>>> dict(name='ziawang', age=23, skills =['stock', 'python'])
{'name': 'ziawang', 'age': 23, 'skills': ['stock', 'python']}                # 正确的写法
>>> 
```

##  字典的items()方法
- dict.items()  返回一个dict_items对象，列表中的元素是由key,  value组成的tuple。用序列拆包的方式可以获取其中的元素
- 返回的元组可以被用来格式化输出，注意下面格式化输出的方式

```python
d = {'name':'ziawang', 'age':23, 'sex':'male'}
l = ['%s==>"%s"  '%item for item in d.items()]
print(''.join(l))
#也可以用着print格式输出
for i in l:
    print(i, sep=', ', end=' .')

--------------------------------------------------
name==>"ziawang"  age==>"23"  sex==>"male"  
name==>"ziawang"   .age==>"23"   .sex==>"male"   .
```


> 意味着，利用dict(sub)可以将这个返回值再编程字典

```python
>>> info = {'name': 'ziawang', 'age': 23, 'skills': ['stock', 'python']}
>>> info.items()
dict_items([('name', 'ziawang'), ('age', 23), ('skills', ['stock', 'python'])])
>>> type(info.items())
<class 'dict_items'>
>>> dict(info.items())
{'name': 'ziawang', 'age': 23, 'skills': ['stock', 'python']}
>>> for i in info.items():
	print(i)

	
('name', 'ziawang')
('age', 23)
('skills', ['stock', 'python'])
>>> 
```

- 遍历这个dict_items对象，可以选择遍历出(key,  value)元组或者将key，value分别便利出来做其他处理

```python
>>> info = {'name': 'ziawang', 'age': 23, 'skills': ['stock', 'python']}
>>> for i in info.items():
	print(i)

	
('name', 'ziawang')
('age', 23)
('skills', ['stock', 'python'])
>>> for i,j in info.items():
	print(i, '===>', j)

	
name ===> ziawang
age ===> 23
skills ===> ['stock', 'python']
>>> 
>>>> type(info.items())
<class 'dict_items'>
```

## 字典的取值
- 建议使用：

	for key in info_dic:
		print(k, info_dic[k])
- 其他：
1.
	for i, j in info_dic.items():   #  items返回的对象是一个dict_items类型对象，其内的元素是封装了key
		print(i, j)					#value对的一个个元组
		

2.
	for   key  in info_dic.keys():
		print(key)
3.
	for value in info_dic.values():
		print(value)


	


## 获取字典的keys
- dict.keys()  返回一个dict_key类型的对象（不是列表，用索引取值会报错）
- list(dict.keys())可以把字典中无序的keys存放到一个有序的列表中。
- sorted(dict.keys())可以将无序的key值进行排序之后存放到列表中并返回该列表

> ps:sorted()不会改动原数列，而是生成一个新的已排序的序列


```python
>>> info
{'name': 'ziawang', 'age': 23, 'skills': ['stock', 'python']}
>>> list(info.keys())
['name', 'age', 'skills']
>>> sorted(info.keys(), reverse=True)
['skills', 'name', 'age']
>>> 
```

## dict methods
 - dict.copy() 返回一个字典的浅复制
	- 直接赋值：其实就是对象的引用（别名）
	- ![](http://https://github.com/ZiaWang/img/blob/master/%E7%9B%B4%E6%8E%A5%E8%B5%8B%E5%80%BC.png?raw=true)
	- 所谓浅复制指：对父对象（一级目录）进行深拷贝，而对子对象（二级目录）不拷贝
	- ![](http://https://github.com/ZiaWang/img/blob/master/%E6%B5%85%E6%8B%B7%E8%B4%9D.png?raw=true)
	- 所谓深复制指：copy模块的deepcopy方法，它完全拷贝了父对象及其子对象
	- ![](http://https://raw.githubusercontent.com/ZiaWang/img/aab8e9b408ad611f12af9e7970833b42fab17152/%E6%B7%B1%E6%8B%B7%E8%B4%9D.png)
	- https://www.runoob.com/w3cnote/python-understanding-dict-copy-shallow-or-deep.html

```python
from copy import deepcopy
info = {'name': 'ziawang', 'age': 23, 'skills': ['stock', 'python']}
info2 = info
info3 = info.copy()
info4 = deepcopy(info)
info['skills'].remove('stock')
info.clear()

print(info)     #原对象
print(info2)    #直接赋值（引用）
print(info3)    #浅拷贝
print(info4)   # 深拷贝

```

> 运行结果

```python
{}
{}
{'name': 'ziawang', 'age': 23, 'skills': ['python']}
{'name': 'ziawang', 'age': 23, 'skills': ['stock', 'python']}
```

- dict.fromkeys(sequence[, value])  用于**快速的**创建一个新的字典
- sequence中的元素作为字典的键
- value为字典所有键对应的初始值

```python
>>> l = [1,2,3,4,5,6]
>>> d = dict.fromkeys(l)
>>> d
{1: None, 2: None, 3: None, 4: None, 5: None, 6: None}
>>> d = dict.fromkeys(l, 'a')      #调用的对象应该是dict，因为对象尚未创建
>>> d
{1: 'a', 2: 'a', 3: 'a', 4: 'a', 5: 'a', 6: 'a'}
>>> 
```

- **key in dict    用于检测键是否在该字典中**

- dict.get(key [,default=None])  返回字典中key对应的value，如果没有这个key，就返回default（默认为None）
	- D.get(k[,d]) -> D[k] if k in D, else d.  
		- d defaults to None.

- dict.setdefault(key, default=None)  返回字典中key对应的value，如果没有这个key，将在字典中添加这个key，value为default，并返回这个value
	- 当创建一个新的value为list或其他可变对象的时候。可以配合append或者extend。
	- **如果key存在则不修改，并返回已有的value值，key不存在就创建（与dict.update()的区别）**
	- setdefault返回的value的id地址与字典中的value的id地址是一样的。

```python
info_dic={'name':'egon','age':18,'sex':'male'}
info_dic.setdefault('hobbies',[1,2])
print(info_dic)
info_dic.setdefault('hobbies',[1,2,3,4,5])
print(info_dic)                 # 并不会输出[1,2,3,4,5]
```

与上面不同，在setdefault()下**对列表中value为listd的对象进行append()，**

```python
info_dic={'name':'egon','age':18,'sex':'male'}
#{'name':'egon','age':18,'sex':'male','hobbies':['study']}
info_dic.setdefault('hobbies',[]).append('study')
#{'name':'egon','age':18,'sex':'male','hobbies':['study','read']}
info_dic.setdefault('hobbies',[]).append('read')
#{'name':'egon','age':18,'sex':'male','hobbies':['study','read','sleep']}
info_dic.setdefault('hobbies',[]).append('sleep')
l=info_dic.setdefault('hobbies',[])

print(l,id(l))
print(id(info_dic['hobbies']))
print(info_dic)

----------------------
['study', 'read', 'sleep'] 3070592950984
3070592950984
{'name': 'egon', 'age': 18, 'sex': 'male', 'hobbies': ['study', 'read', 'sleep']}

```

- dict.values()   返回一个字典value构成的列表，只不过类型是dict_values
- dict.keys() 返回一个字典key构成的列表，只不过类型为dict_keys
- 上面的两个函数的返回值可以用list()函数转换成list

```python
>>> info = {'name': 'ziawang', 'skills': ['python', 'stock']}
>>> info.get('nam', '没有这个key')
'没有这个key'
>>> info.setdefault('hometown', 'Anhui')
'Anhui'
>>> info
{'name': 'ziawang', 'skills': ['python', 'stock'], 'hometown': 'Anhui'}
>>> info.values()
dict_values(['ziawang', ['python', 'stock'], 'Anhui'])
>>> type(info.values())
<class 'dict_values'>
>>> info.keys()
dict_keys(['name', 'skills', 'hometown'])
>>> list(info.keys())
['name', 'skills', 'hometown']
>>> list(info.values())
['ziawang', ['python', 'stock'], 'Anhui']
```

- dict.update(dict2)  更新字典，将原字典中的key-value修改或者将一个新的字典添加到指定的dict中
- **注意，这个方法时在原字典的基础上对原字典进行修改，此函数无返回值类似list.extends(l)**

```python
>>> info = {'name':'ziawang'}
>>> info_anthor = {'age':23, 'city':'Beijing'}
>>> info_new = info.update(info_anthor)
>>> info_new
>>> info
{'name': 'ziawang', 'age': 23, 'city': 'Beijing'}
>>> 
```

- dict.pop(key[, default])  返回key对应的value，并删除指定的key在dict中对应的key:value对，其中key为必要参数。当default指定值的时候，如果字典中没有key，那么就会返回default指定的值。 如果没有指定default，而且字典中也没有该key，那么就会报错。
- dict.popitem()   随机的返回并删除一个key-value对

```python
>>> info = {'name': 'ziawang', 'age': 23, 'city': 'Beijing'}
>>> info.pop('name', '没有这个key!!')
'ziawang'
>>> info.pop('name', '没有这个key!!')
'没有这个key!!'
>>> info.pop('name', '没有这个key!!')
'没有这个key!!'
>>> info.pop('name')
Traceback (most recent call last):
  File "<pyshell#50>", line 1, in <module>
    info.pop('name')
KeyError: 'name'
>>>> info.popitem()
('city', 'Beijing')
>>> info
{'age': 23}
>>> 
```

## 无穷嵌套字典

```
>>> a = {}
>>> b = {}
>>> a['b'] = b
>>> a
{'b': {}}
>>> b['a'] = a
>>> b
{'a': {'b': {...}}}
>>> a
{'b': {'a': {...}}}
>>> 
```







