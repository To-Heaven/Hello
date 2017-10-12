# collections
[点击查看我整理的namedtuple思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/namedtuple.png?raw=true)
[点击查看我整理的deque思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/deque.png?raw=true)
[点击查看我整理的OrderedDict思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/OrderedDict.png?raw=true)

> specialize	专门  
> commas		逗号  
> consist		组成  	
> eliminate		消除  
> outdated	过时的  
> verbose		冗长的，落得  
> per-instance	基于实例的  
> map			比对   
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
from collections import namedtuple

s = namedtuple('S', ['def', 'global', 'haha'])
a = s(1, 2, 3)
print(a)   # 报错   

# -------------------------------------------
from collections import namedtuple

s = namedtuple('S', ['def', 'global', 'haha'], rename=True)
a = s(1, 2, 3)
print(a)  # 不报错但是fieldname被修改成合法的了    S(_0=1, _1=2, haha=3)


```

- Named tuple instance do not have per-instance dictionaries, so they are lightweight and require no more memory than regular tuples.（命名元组namedtuple的实例是轻量级的，比tuple占用更小内存，而且他的实例不是基于字典的实例）

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
> 通过namedtuple创建的tuple 的子类继承了tuple的所有方法    
> 常用的有  t.index(value)   e.count(value)

- somenamedtuple._make(iterable)
	- makes a new instance from an existing sequence or iterable（不会改变原先的对象）
	-但是要注意参数的个数要一一对应

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


- To convert a dict to a named tuple ,use the double-star-oprator (Unpacking Argument Lists)

```python
>>> point = namedtuple('point', 'x  y')
>>> d = {'x':100, 'y':200}
>>> point(**d)
point(x=100, y=200)
>>> 
```


## Somenamedtuple Variables

- somenamedtuple._source
	- A string with the pure Python source code used to create the named tuple 
	- ?????????????
- somenamedtuple._fields
	- tuple of string  listing field names.
	- Userful for introspection and for creating new named tuple tyes from existing named tuples（利用现有的field_name创建新的named tuple）

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
import time
from collections import deque

d = deque(maxlen=5)

for i in range(10):
    d.append(i)
    print(d)
    time.sleep(1)

-------------------------------------------
# 结果
deque([0], maxlen=5)
deque([0, 1], maxlen=5)
deque([0, 1, 2], maxlen=5)
deque([0, 1, 2, 3], maxlen=5)
deque([0, 1, 2, 3, 4], maxlen=5)
deque([1, 2, 3, 4, 5], maxlen=5)
deque([2, 3, 4, 5, 6], maxlen=5)
deque([3, 4, 5, 6, 7], maxlen=5)
deque([4, 5, 6, 7, 8], maxlen=5)
deque([5, 6, 7, 8, 9], maxlen=5)
``` 

### deque.Method
- deque.append(value)
	- 将value从右边插入deque对象中，左进右出
- deque.appendleft(value)
	- 将value从左边插入的deque对象中，右进左出
	
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
	- 把iterable的值按照顺序插入到deque中
- deque.extendleft(iterable)
	- Note: the series of left appends results in reversing the order of elements in the iterable argument（当使用deque.extendleft()方法的时候，传入的iterbale会被**倒序插入到deque的左边**）

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
-  deque.rotate(n)
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

## deque object variables

- deque.maxlen
	- Maximum size of a deque 
	- Note: None if unbounded



### other bulitin functions
- deques support iteration, pickling, len(d), reversed(d), copy.copy(d), copy.deepcopy(d), membership testing with the '  in   '  operator, and subscript references such as d[-1]
- **Don't support slice!!!!!!!!!!!!**（支持索引，但不支持切片）
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


## Counter objects(sunclass of dict, but some methods are different)
> tally   计数器   
> rapid 快速的

-  a counter object is provided for rapid tallies.

-  Counter object
	- `c = Counter([iterable-or-mapping-or-keywords_arguments-other_counter])`
	- a counter is a subclass of dict for counting **hashable objects**
	- futures
		- used for counting hashable objects
		- counters are unordered collection as dicts
			- keys ---> the elements to count 
			- values ---> the count number for elements, can be a integer or zero and negative counts

```python
from collections import Counter

c_empty = Counter()                        # 创建一个空counter对象

c_iter = Counter('abcabcabc')              # 通过可迭代对象创建counter对象

c_map = Counter({'a': 1, 'b': 2, 'c': 3})  # 通过映射关系创建counter对象

c_kw = Counter(a=1, b=2, c=3)              # 通过关键字传参创建counter对象

c_pairs = Counter(dict([('a', 1), ('b', 2), ('c', 3)]))  # 通过pairs数据结构创建counter对象

```

## Methods of Counter Objects
- c.elements()
	- returns : iterator. elements repeating each as many times as its count
	- note:
		1. elements are returned in arbitrary order.
		2. elements wil be ingored if the count of this element less than one 

- c.most_common([n])
	- arg : n(int).  the first 'n' most common elements,  is also the length of the returned list
		-   returns all elements in the counter if n is omitted or None
	- return : list.  list of tuples which contain the element and their counts

- substract([iterable-or-mapping-or-keywords_arguments-other_counter])
	- substract counts of elements from the specified iterable/mapping or other objects
	- return : None
	- note:
		1.  theis methods just substracts counts instead of replacing the element from counter
		2.  inputs and outputs may be zero or negative

## Methods between Counter  object and dict object 
- Counter object **do not support the `fromkeys(iterable)` methods**
- `d.update([iter-or-mapping])` of Counter obejct is just adds counts instead of replacing them. 
	- return : None
	- **iterable is expected to be a sequence of elements, not a sequence of (key, value) pairs.**


## mathematical operations of Counter obejects
- `c1 + c2`
- `c1 - c2`
- `c1 & c2`
- `c1 | c2`
- `-c`
- `+c`
- note:
	- `c1.update(c2)` <==> `c1 + c2`  等价
	- `c1.subtract(c2)`  !<==> `c1+ c2`   不等价 


```python
from collections import Counter

c_part1 = Counter(a=1, b=2, c=3)

c_part2 = Counter(a=1, b=3, d=3)

print('c_part1 & c_part2 : ', c_part1 & c_part2)
print('c_part1 | c_part2 : ', c_part1 | c_part2)
print('c_part1 + c_part2 : ', c_part1 + c_part2)
print('c_part1 - c_part2 : ', c_part1 - c_part2)

c_part1.subtract(c_part2)
print('subtract : ', c_part1)

c_part1.update(c_part2)
print('update : ', c_part1)
```


```python
c_part1 = Counter(a=1, b=2, c=3)

c_part2 = Counter(a=1, b=3, d=3)

print(c_part1 - c_part2)

c_part1.subtract(c_part2)
print(c_part1)
# ---------------------------------------
Counter({'c': 3})
Counter({'c': 3, 'a': 0, 'b': -1, 'd': -3})
```

```python
c_part1 = Counter(a=1, b=2, c=3)

c_part2 = Counter(a=1, b=3, d=3)

print(c_part1 + c_part2)

c_part1.update(c_part2)
print(c_part1)
# ---------------------------------
Counter({'b': 5, 'c': 3, 'd': 3, 'a': 2})
Counter({'b': 5, 'c': 3, 'd': 3, 'a': 2})
```


## defaultdict object
> 注意，在python2.5以下版本（包括2.5）是不支持defaultdict的，这就需要自己创建一个defaultdict类

- defaultdict is a subclass of the built-in dict class
	- default是dict的子类，除了覆盖了的方法，其他方法和dict方法相同
	
- defaultdict([default_factory [ mapping-or-iterable-or-list_pairs]])
	-  args:
		-  default_factory: 
			-  a builtin_factory_method or method defined by user without arguments
			- default None
		- [ . . . ]  
			- arguments  passed to the dict constructor, including keyword arguments.
## defaultdict.__missing__（key）
-  先看官方文档

```python
__missing__(key) # Called by __getitem__ for missing key; pseudo-code:
  if self.default_factory is None: raise KeyError((key,))
  self[key] = value = self.default_factory()
  return value
```

- if  default_factory is None,  raises a KeyError exception with the key
- If default_factory is not None, it provide a default value for the given key, and return the value 
- note
	- This method is called by the __getitem__() method of the dict class when the requested key is not found

- **variables for defaultdict object**
	- default_factory	
		- 可以用来指定和修改defaultdict的default_factory属性值

```python
from collections import defaultdict

d_list = defaultdict(list)
print(d_list['a'])

d_list.default_factory = int
print(d_list)
```

- note
	- 当__geiitem__()没有获取到对应的值之后，defaultdict的__missing__(key)方法会通过default_factory将key及对应default_factory返回值的pair存放到defaultdict对象

``` python
>>> d_list = defaultdict(list)
>>> 'haha' in  d_list
False
>>> d_list['haha']
[]
>>> 'haha' in  d_list
True
>>>
```

## default_factory of defaultdict
- `list` group a sequence of key-value pairs into a dictionary of lists
	- use the `list.append(value)` method
- `int`  useful for counting
	- use the `+=` mathematical operation
- `set` useful for building a dictionary of sets（去重）
	- use the `s.add(value)`method

- 自定义default_factory
	- 注意：用户自定义的default_factory属性值必须是没有参数的

```python
from collections import defaultdict

def my_default_factory():
    return 'default value'

d_my = defaultdict(my_default_factory)
print(d_my)
print(d_my['key'])

# =====================================
from collections import defaultdict

count = defaultdict(int)

print(count)

l = ['a', 'b', 'a', 'c', 'b', 'd', 'c', 'e']
for i in l:
    count[i] += 1

print(count)
```

## 自定义defaultdict类兼容python2.5以下版本

```python
try:
    from collections import defaultdict
except ImportError:
    class defaultdict(dict):
        def __init__(self, default_factory=None, *args, **kwargs):
            super().__init__()
            if (default_factory is not None) and\
                (not hasattr(default_factory, '__call__')):
                raise TypeError('first argument must be callable')
            self.default_factory = default_factory

        def __getitem__(self, key):
            try:
                return dict.__getitem__(key)
            except KeyError:
                return self.__missing__(key)

        def __missing__(self, key):
            if self.default_factory is None:
                raise KeyError(key)
            self[key] = value = self.default_factory()
            return value

        def __reduce__(self):
            if self.default_factory is None:
                args = tuple()
            else:
                args = self.default_factory
            return type(self), args, None, None, self.items()

        def copy(self):
            return self.__copy__()

        def __copy__(self):
            return type(self)(self.default_factory, self)

        def __deepcopy__(self, memo):
            import copy
            return type(self)(self.default_factory,
                              copy.deepcopy(self.items()))

        def __repr__(self):
            return 'defaultdict({}, {})'.format(self.default_factory, 
                                                dict.__repr__(self))
        
```





> ordered           规则的，有序的

## OrderDict Object
- ordereddict = collections.OrderedDict([items])
	- Return an instance of a dict subclass, **supporting the usual dict methods**
	- An OrderedDict is a dict that remembers the order that keys were first inserted.
		- OrderedDict会记住先插入到其中的key
	- If a new entry overwrites an existing entry, the original insertion position is left unchanged.
		- __如果插入了相同的key，就会在原key位置上进行覆盖，位置不变_-
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
		- DEFAULT Trye
			- :moved to the right end if last is true
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
