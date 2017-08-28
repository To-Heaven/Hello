> implement	实现  
> specialize	专门  
> container	容器  
> separated        分开的  
> whitespace	空白符，空字符，空白格  
> commas		逗号  
> identifier		标识符  
> valid		有效的  
> underscore	下划线  
> letter		字母  
> digit			数字  
> consist		组成  
> keyword 	关键字  
> convert		修改	
> eliminate		消除  
> duplicate		重复，复制  
> option		选项  
> outdated	过时的  
> definition	定义，解释  
> verbose		冗长的，落得  
> per-instance	基于实例的  
> map			比对   
> corresponding	对应的  
> introspection		内省  
## collections模块提供除了python内置的dict, list , set, tuple容器数据类型之外的其他数据容器
- namedtuple
- deque
- Counter
- OrderedDict
- defaultdict
- UserDict
- ChainMap
- UserList
- UserString


## namedtuple
- namedtuple最牛逼的用处就是利用它**创建一个只有属性的类**， 不过属性是不能变的，只能是创建时指定，for example（扑克牌）

```python
 import collections
>>> course = collections.namedtuple('Course', 'price  name')
>>> python = course('1', 'python')
>>> python.period = '6 month'
Traceback (most recent call last):
  File "<pyshell#4>", line 1, in <module>
    python.period = '6 month'
AttributeError: 'Course' object has no attribute 'period'
>>>

```



- collections.namedtuple(typename, field_names, *, verbose=False, rename=False, module=None)
- returns a new tuple subclass named typename. The new subclass is used to  create tuple-like objects that have fields accessible by attribute lookup as well as being indexable and iterable（就是创建的对象可以通过对象访问属性的方式获取对应的值，同样还可一用索引，因为他是tuple的子类）。
- instances of the subclass also have a helpful docstring and helpful __repr__()method which list the tuple contents in a name=value format （__repr__方法可以将tuple中的内容按照name=value的格式获取出来）。
```python
>>> tu = collections.namedtuple('my_tuple', ['x', 'y'])
>>> tu
<class '__main__.my_tuple'>
>>> t = tu(1, 2)
>>> t
my_tuple(x=1, y=2)
>>> t.x
1
>>> t[1]
2
>>> t.__repr__
<bound method my_tuple.__repr__ of my_tuple(x=1, y=2)>
>>> t.__repr__()
'my_tuple(x=1, y=2)'
>>> 
```


- field_names are a sequence of strings such as ['x', 'y'].field_names can be a single string with each fieldname separated by whitespace and/or commas, for example 'x  y' or  'x,  y'（field_names的书写格式，可以是写在一个序列中的几个字符串，那么问题来了，字符串也是序列，所以龟叔后面继续说，字符串也可以传入field_names，不过要用空格隔开或者用够好隔开，反正就是不能连接在一起）。
- Any valid Python identifier may be used for a fieldname except for names starting with an underscore .Valid identifers consist of letters ,digits,and underscores but do not start with a figit or underscore . and can not be a keyword such as class, for ,return, global, pass, or raise （龟叔说了下fieldname的命名方式，和标识符的命名方式一样，不能以数字和下划线开头的数字，字母，下划线的任意组合，而且不能和关键字相同）
- if rename is true ,invalid fieldnames are automatically replaced with positional names.for example:
（就是当field_name不生效的时候，如果rename是True，就会帮我们把无效的field_name改成下划线加上对应索引的形式 ）['abc', 'def', 'ghi', 'abc']  is converted to ['abc', '_1', 'ghi', '_3']
```python
>>> s = collections.namedtuple('ss', ['def', 'abc', 'haha'])
Traceback (most recent call last):
  File "<pyshell#17>", line 1, in <module>
    s = collections.namedtuple('ss', ['def', 'abc', 'haha'])
  File "D:\Python36\lib\collections\__init__.py", line 403, in namedtuple
    'keyword: %r' % name)
ValueError: Type names and field names cannot be a keyword: 'def'				# 3.6直接报错了
```

- Named tuple instance do not have per-instance dictionaries, so they are lightweight and require no more memory than regular tuples.（命名元组namedtuple的实例是轻量级的，比tuple占用更小内存，而且他的实例不是基于实例的字典）

```python
>>> from collections import namedtuple
>>> name = namedtuple('name_tuple', ['first_name', 'last_name'])
>>> ziawang = name('wang', last_name='zihao')
>>> ziawang.first_name
'wang'
>>> ziawang.last_name
'zihao'
>>> ziawang['first_name']
Traceback (most recent call last):
  File "<pyshell#17>", line 1, in <module>
    ziawang['first_name']
TypeError: tuple indices must be integers or slices, not str
>>> ziawang[0]
'wang'
>>> ziawang[1]
'zihao'
>>> first, last = ziawang
>>> first
'wang'
>>> last
'zihao'
>>> ziawang.first + ziawang.last
Traceback (most recent call last):
  File "<pyshell#23>", line 1, in <module>
    ziawang.first + ziawang.last
AttributeError: 'name_tuple' object has no attribute 'first'
>>> ziawang.first_name + ziawang.last_name
'wangzihao'
>>> 
```

### somenamedtuple.Methods
- somenamedtuple._make(iterable)
	- makes a new instance from an existing sequence or iterable（不会改变原先的对象）

```python
>>> ziawang.first_name + ziawang.last_name
'wangzihao'
>>> l = ['wang', 'zihao']
>>> name._make(l)                                                       #  注意，此处的name是上例中的name 实例
name_tuple(first_name='wang', last_name='zihao')
>>> s = name._make(l)
>>> s.first_name
'wang'
>>> 

```

 - somenamedtuple._asdict()
	 - return a new OrderedDict which maps field names to their corresponding values

```python
>>> name = namedtuple('name_tuple', ['first_name', 'last_name'])
>>> ziawang = name(first_name='wang', last_name='zihao')
>>> ziawang
name_tuple(first_name='wang', last_name='zihao')
>>> ziawang._asdict()
OrderedDict([('first_name', 'wang'), ('last_name', 'zihao')])
>>> 
```

- somenamedtuple._replace(**kwargs)
	- return a new instance of the named tuple replacing secified fields with new values

```python
# 接上例，原命名元组并为改变
>>> ziawang._replace(first_name='liu', last_name = 'xiaoqian')
name_tuple(first_name='liu', last_name='xiaoqian')
>>> ziawang
name_tuple(first_name='wang', last_name='zihao')
>>> 
```

- somenamedtuple._source
	- A string with the pure Python source code userd to create the named tuple 
	- ?????????????
- somenamedtuple._fields
	- tuple of string  listing field names.Userful for introspection and for creating new named tuple tyes from existing named tuples（利用现有的field_name创建新的named tuple）

```python
>>> point = namedtuple('point', 'x  y')
>>> p = point(10, 20)
>>> p._fields
('x', 'y')
>>> Color = namedtuple('Color', 'red blue green')
>>> Pixel = namedtuple('Pixel',p._fields + Color._fields)
>>> Pixel(11,22,128,255,0)
Pixel(x=11, y=22, red=128, blue=255, green=0)
>>> 
```

- To convert a dict to a named tuple ,use the double-star-oprator (Unpacking Argument Lists)

```python
>>> point = namedtuple('point', 'x  y')
>>> d = {'x':100, 'y':200}
>>> point(**d)
point(x=100, y=200)
>>> 
```


> bounded		有限制的  
> tracking		追踪  
> transaction	事物  
> corresponding	对应的  
> rotate		轮换，交替  


## deque object
- collections.deque([iterable[maxlen]])
	- return a new deque object initialized left-to-right(using append()) with data from iterable. if iterable is not specified,the new deque is empty
	- if  maxlen is not specified or is None, the deque is bounded to the specified maximum length .
	- when new items are added , a corresponding number of items are discarded from the oppisite end.they are also useful for tracking transactions and other pools of data where only the most recent activity is of intersted
	- Note: if length of iterble bigger than maxlen. then create a deque from the left

```python
from collections import deque
l = list(range(10))
l_deque = deque(l, 5)
print(l)
print(l_deque)

-------------------------------------------
# 结果
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
deque([5, 6, 7, 8, 9], maxlen=5)
``` 

### deque.Method
- deque.append(value)
- deque.appendleft(value)

```python
>>> l = list(range(10))
>>> from collections import deque
>>> l_deque = deque(l, 5)
>>> l_deque
deque([5, 6, 7, 8, 9], maxlen=5)
>>> l_deque.append((0,0,0))
>>> l_deque
deque([6, 7, 8, 9, (0, 0, 0)], maxlen=5)
>>> l_deque.appendleft('appendleft')
>>> l_deque
deque(['appendleft', 6, 7, 8, 9], maxlen=5)
>>> 
```

- deque.clear()
- deque.copy()
	- return a shallow copy of the deque

- deque.count(element)
	- return the number of deque equal to elelment
- deque.extend(iterable)
- deque.extendleft(iterable)
	- Note: the series of left appends results in reversing the order of elements in the iterable argument（当使用dque.extendleft()方法的时候，传入的iterbale会被倒序插入到deque的左边）

```python
>>> from collections import deque
>>> q = deque(maxlen = 5)
>>> q.extendleft([1,2,3,4])
>>> q
deque([4, 3, 2, 1], maxlen=5)

```

- deque.index(element[start, [end]])
	- return the first match or raise ValueError if not found

- insert(index, value)
	- insert x into the deque at position index
	- if the insertion would cause a bounded deque to grow beyond maxlen, an IndexError is raised

```python
>>> from collections import deque
>>> q = deque(maxlen = 5)
>>> q.extendleft([1,2,3,4])
>>> q
deque([4, 3, 2, 1], maxlen=5)
>>> q.append(5)
>>> q
deque([4, 3, 2, 1, 5], maxlen=5)
>>> q.append(6)
>>> q
deque([3, 2, 1, 5, 6], maxlen=5)
>>> q.insert(2, 7)
Traceback (most recent call last):
  File "<pyshell#14>", line 1, in <module>
    q.insert(2, 7)
IndexError: deque already at its maximum size
>>> 

```

- deque.pop()
	- remove and return an element from the right side of the deque.
	- Note: if no elements are present , raise an IndexError 

- deque.popleft()
	- remove and return an element from the left side of the deque.
	- Note: if no elements are present , raise an IndexError 

```python
>>> q
deque([3, 2, 1, 5, 6], maxlen=5)
>>> q.pop()
6
>>> q
deque([3, 2, 1, 5], maxlen=5)
>>> q.popleft()
3
>>> q
deque([2, 1, 5], maxlen=5)
>>> 
``` 

- deque.remove(value)
	- remove the first occurence of value from the deque ,if not found , raises a ValueError

- deque.reverse()
-  deque.rorate(n)
	-  rotate the deque n steps to the right.
	- if n is negative , rotate to the left . 
	- rotate one step to the right is equivalent to : deque.appendleft(deque.pop())

```python
>>> q
deque([5, 6, 7, 8, 9], maxlen=5)
>>> q.appendleft(q.pop())
>>> q
deque([9, 5, 6, 7, 8], maxlen=5)
>>> q.rotate(3)
>>> q
deque([6, 7, 8, 9, 5], maxlen=5)
>>> q.rotate(5)
>>> q
deque([6, 7, 8, 9, 5], maxlen=5)
>>> q.rotate(4)
>>> 1
1
>>> q
deque([7, 8, 9, 5, 6], maxlen=5)
>>> q.rotate(1)
>>> q
deque([6, 7, 8, 9, 5], maxlen=5)
>>> q.appendleft(q.pop())
>>> q
deque([5, 6, 7, 8, 9], maxlen=5)
>>> 

```

- deque.maxlen()
	- Maximum size of a deque 
	- Note: None if unbounded

### other bulitin functions
- deques support iteration, pickling, len(d), reversed(d), copy.copy(d), copy.deepcopy(d), membership testing with the '  in   '  operator, and subscript references such as d[-1]
- **Don't support slice!!!!!!!!!!!!**
```python
>>> d = deque(maxlen = 5)
>>> d
deque([], maxlen=5)
>>> d.extendleft([6,5,4,3,2,1])
>>> d
deque([1, 2, 3, 4, 5], maxlen=5)
>>> d[0:3]                                                           # 不支持切片slice!!!!!!!!!!!1
Traceback (most recent call last):
  File "<pyshell#44>", line 1, in <module>
    d[0:3]
TypeError: sequence index must be integer, not 'slice'
>>> d[3]
4
>>> len(d)
5
>>> reversed(d)
<_collections._deque_reverse_iterator object at 0x000002A35E97ECC8>
>>> 
```

## defaultdict



> ordered           规则的，有序的

## OrderDict
- DrderedDcit objects
- ordereddict = collections.OrderedDict([items])
	- Return an instance of a dict subclass, supporting the usual dict methods
	- An OrderedDict is a dict that remembers the order that keys were first inserted.
		- OrderedDict会记住先插入到其中的key
	- If a new entry overwrites an existing entry, the original insertion position is left unchanged.
		- 如果插入了相同的key，就会在原key位置上进行覆盖，位置不变
	-   Deleting an entry and reinserting it will move it to the end.
		-   改变key的位置可以通过这种方式

```python
>>> d = OrderedDict([('name','ziawang'),('age', 21)])
>>> d
OrderedDict([('name', 'ziawang'), ('age', 21)])
>>> d['a'] = 'a'
>>> d
OrderedDict([('name', 'ziawang'), ('age', 21), ('a', 'a')])
>>> d['name'] = 'wangzihao'
>>> d
OrderedDict([('name', 'wangzihao'), ('age', 21), ('a', 'a')])
>>> d['age'] = 18
>>> d
OrderedDict([('name', 'wangzihao'), ('age', 18), ('a', 'a')])
>>> del d['name']
>>> d
OrderedDict([('age', 18), ('a', 'a')])
>>> d['name'] = 'ziawang'
>>> d
OrderedDict([('age', 18), ('a', 'a'), ('name', 'ziawang')])
>>> 
```

> 先进先出    FIFO  ——  first in first out
> 先进后出    LIFO  ——  last  in first  out

### OrderedDict  Methods
- od.popitems(last=True)
	- The popitem() method for ordered dictionaries returns and removes a (key, value) pair. The pairs are returned in LIFO order if last is true or FIFO order if false

```python
>>> od = OrderedDict([('a', 1), ('b', 2), ('c', 3), ('d', 4)])
>>> od.popitem(last=True)
('d', 4)
>>> od
OrderedDict([('a', 1), ('b', 2), ('c', 3)])
>>> od.popitem(last=False)
('a', 1)
>>> 
```

- od.move_to_end(key, last=True)
	- Move an existing key to either end of an ordered dictionary.
		- DEFAULT:moved to the right end if last is true
		- moved to the beginning if last is false
	-  Raises KeyError if the key does not exist:


```python
>>> od = OrderedDict([('a', 1), ('b', 2), ('c', 3), ('d', 4)])
>>> od
OrderedDict([('a', 1), ('b', 2), ('c', 3), ('d', 4)])
>>> od.move_to_end('a', last=True)
>>> od
OrderedDict([('b', 2), ('c', 3), ('d', 4), ('a', 1)])
>>> od.move_to_end('a', last=False)
>>> od
OrderedDict([('a', 1), ('b', 2), ('c', 3), ('d', 4)])
>>> 

```


- 给字典排序

```python

>>> od = OrderedDict(sorted(d.items(), key=lambda i :i[1]))
>>> od
OrderedDict([('e', 0), ('d', 1), ('c', 2), ('b', 3), ('a', 4)])
>>> 
```

- 自定义有序字典！！！

```python


class MyOrderedDict(OrderedDict):
    def __setitem__(self, key, value):
        if key in self:
            del self[key]
        OrderedDict.__setitem__(self, key, value)


m = MyOrderedDict([('a', 1),('b', 2), ('c', 3)])
m['a'] = 0
print(m)

---------------------------------------------------
MyOrderedDict([('b', 2), ('c', 3), ('a', 0)])
```


























https://docs.python.org/3/tutorial/controlflow.html#tut-unpacking-arguments