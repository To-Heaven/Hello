# 数据处理进阶

## 筛选数据
#### 筛选列表数据
- 准备数据

```python
>>> from random import randint
>>> data = [randint(-5, 5) for _ in range(10)]
>>> data
[-3, 0, -3, 5, -4, 2, -1, 2, 2, -4]
```

###### 屌丝版for遍历
```python
>>> res = []
>>> for i in data:
	if i>=0:
	    res.append(i)

	    
>>> res
[0, 5, 2, 2, 2]
```


###### 中级版filter

```python

>>> res = list(filter(lambda x: x>=0, data))
>>> res
[0, 5, 2, 2, 2]
```

###### 高级版推导式

```python
>>> res = [x for x in data if x>=0]
>>> res
[0, 5, 2, 2, 2]
```


#### 筛选字典数据
- 数据准备

```python
>>> data = {x: randint(1, 100) for x in range(1, 10)}
>>> data
{1: 70, 2: 21, 3: 86, 4: 71, 5: 32, 6: 8, 7: 2, 8: 25, 9: 55}
>>> 
```

###### 屌丝版for遍历
- 略

###### 中级版filter

```python
>>> res = dict(filter(lambda item: item[1]>50, data.items()));
>>> res
{1: 70, 3: 86, 4: 71, 9: 55}
>>> 
```


###### 高大上推导式

```python
>>> res = {x: y for x, y in data.items() if y>50}
>>> res
{1: 70, 3: 86, 4: 71, 9: 55}
>>> 
```

#### 筛选集合数据
- 准备数据

```python
>>> data = {randint(-5, 5) for i in range(10)}
>>> data
{0, 4, 5, -1, -5, -4, -2}
```

###### 屌丝版for循环

```python
>>> res = set()
>>> for val in data:
	if val>0:
	    res.add(val)

	    
>>> res
{4, 5}
```

###### 中级版filter

```python
>>> res = set(filter(lambda x: x>0, data))
>>> res
{4, 5}
```

###### 高大上版推导式
```python
>>> res = {val for val in data if val>0}
>>> res
{4, 5}
```

## 使用命名元组
- 单纯的使用索引去访问元组中的元素可读性极差，而且容易弄错，使用命名元组吧，namedtuple本质上是创建了一个只有属性的类，我们可以像访问对象属性的方式去访问元组中的元素

```python
>>> from collections import namedtuple
>>> Student = namedtuple("Student", ["name", "age", "classroom"])
>>>> ziawang = Student("ziawang", 18, "三年二班")
>>> ziawang
Student(name='ziawang', age=18, classroom='三年二班')
```

- 也可以传入一个字典

```
>>> info_ziawang = {
	"name": "ziawang",
	"age": 18,
	"classroom": "三年二班"
	}
>>> ziawang = Student(**info_ziawang)
>>> ziawang
Student(name='ziawang', age=18, classroom='三年二班')
>>> ziawang.name
'ziawang'
>>> ziawang.age
18
>>> ziawang.classroom
'三年二班'
```


- python中有一个enum模块支持枚举，也可以实现相同的功能，但是使用起来没有namedtuple轻巧方便
- 再放一个可以发散思维的方式，在没有上述两种方法的情况下，我们还可以这样做	

```python
>>> name, age, classroom = range(3)
>>> ziawang = ("ziawang", 18, "三年二班")
>>> ziawang[name]
'ziawang'
>>> ziawang[age]
18
>>> ziawang[classroom]
'三年二班'
>>> 
```


## 使用collections.Counter统计文章中中元素重复的次数

```python
from collections import Counter
from re import findall

with open("a.txt", 'r') as rf:
	data = rf.read()
	words = article.findall(pattern='\W+', string=data)
	wordCounter = Counter(words)
	top5 = wordCounter.most_common(5)
```

## 对字典排序
#### 按照value进行排序
























