[点击查看我整理的集合思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/set-2.png?raw=true)

## set
## frozenset

> duplicate       复制  
> compute        计算   

- A set object is an  **unordered ** collection of distinct hashable objects
- common uses
	- membership testing    'in ' and  ' not in '
	- removing duplicates from a sequence
	- computing mathematical operations 
		- & 	intersection
		- |	union
		- ^	
		-   ‘ -’ difference
		- Do not support ' + '
	- len(set)
	- iterable
		- `for element in set` 

```python
>>> s1 = {1, 2, 3, 4, 5, 6}
>>> s2 = {4, 5, 6, 7, 8, 9}
>>> s1 & s2
{4, 5, 6}
>>> s1 | s2
{1, 2, 3, 4, 5, 6, 7, 8, 9}
>>> s1 ^ s2
{1, 2, 3, 7, 8, 9}
>>> s1 - s2
{1, 2, 3}
>>> s2 - s1
{8, 9, 7}
>>> s1 + s2
Traceback (most recent call last):
  File "<pyshell#13>", line 1, in <module>
    s1 + s2
TypeError: unsupported operand type(s) for +: 'set' and 'set'
>>> s1.intersection(s2)
{4, 5, 6}
>>> s1.union(s2)
{1, 2, 3, 4, 5, 6, 7, 8, 9}
>>> s1.symmetric_difference(s2)
{1, 2, 3, 7, 8, 9}
>>> s1.difference(s2)
{1, 2, 3}
>>> s2.difference(s1)
{8, 9, 7}
>>> 

```



- **Note**:
	- sets do not record element position or order of insertion
	- sets do not support indexing, slicing, or other sequence-like behavior.

- **set  and  forzenset**
	- set:
		- mutable
		- unhashable !!! it has no hash value
			- cannot be used as either a dictionary key or as an element of another set
		- contents can be changed
	- forzenset
		- immutable
		- hashable
		- contents cannot be altered after it is created
		- can be used as a dictionary key or as an element of another set
	- operations that mix set instances with frozenset return the type of the first operand.

```python
>>> frozenset('abc') | set('bcd')
frozenset({'a', 'c', 'b', 'd'})
>>> set('bcd') | frozenset('abc')
{'a', 'c', 'b', 'd'}
>>> 


```

## set([iterbale])
## forzenset([iterable])

- the elements of the iterable must be hashable 
	- To represent sets of sets, the inner sets must be frozenset objects. 
	- If iterable is not specified, a new empty set is returned.

- methods of set and frozenset  object  s
	- len(s)
	- element in s
		- element not in s
	-  isdisjoint(other)
		-  Return True 
			-  if the set has no elements in common with other. Sets are disjoint if and only if their intersection is the empty set.
			- 当且仅当两个set的&值是一个空集合的时候

	- issubset   <==>  set <= other
		- Test whether every element in the set is in other.
	- set < other
		-  Test whether the set is a proper subset of other, that is, set <= other and set != other.
	- issuperset  <==> set >= other
		-   Test whether every element in other is in the set.
	-  set > other
		-  Test whether the set is a proper superset of other, that is, set >= other and set != other.
	- symmetric_difference(other)   a ^ b
		-  Return a new set with elements in either the set or other but not both.
	- set.copy()
		- Return a new set with a shallow copy of s.
	- Note: 
		- any two nonempty disjoint sets are not equal and are not subsets of each other,
		
```python
>>> a = {1,2,3}
>>> b = {4,5,6}
>>> a == b
False
>>> a > b
False
>>> a < b
False
>>> 

``` 

> 同时和多个set操作， 返回新的set


	- union(others)    <==>  a|b|c|d...
		- Return a new set with elements from the set and all others.
	- intersections(others)   <==>  a&b&c&d...
		- Return a new set with elements common to the set and all others.
	- difference(others)   <==>   a - b - c - d ....
		-  Return a new set with elements in the set that are not in the others.

> 对set本身进行操作，无返回值， 但是对于frozenset，会创建一个新的frozenset并赋值给该对象


- update(*others)  <==>  a |= b | c | d....		(注意，a = a | b | c | d...在运算过程中不断创建新set)
	- Update the set, adding elements from all others.
- intersection_update(*others)  <==>  a &= b & c & d...
	- Update the set, keeping only elements found in it and all others.
- difference_update(*others)   <==>  a -= b | c | d ...
	-  Update the set, removing elements found in others.
- symmetric_difference_update(other)   <==>  a ^= b
	-  Update the set, keeping only elements found in either set, but not in both.
- **注意**：
	- 这些函数的参数可以是一个可迭代对象

```python
>>> a &= b
>>> a
frozenset({'c', 'b'})
>>> a
frozenset({'c', 'b'})
>>> b
{'c', 'b', 'd'}
>>> b.update(['a', 'e'])
>>> b
{'e', 'a', 'c', 'b', 'd'}
>>> b.update('h')
>>> b
{'e', 'a', 'h', 'c', 'b', 'd'}
>>> b.update('asdfghj')
>>> b
{'e', 'a', 'h', 'f', 's', 'g', 'c', 'b', 'j', 'd'}
>>> a
frozenset({'c', 'b'})
>>> a.update('asdf')
Traceback (most recent call last):
  File "<pyshell#79>", line 1, in <module>
    a.update('asdf')
AttributeError: 'frozenset' object has no attribute 'update'
>>> a |= {1,2,3}
>>> a
frozenset({1, 2, 3, 'c', 'b'})
>>> 
```


- set 和forzenset进行比较
	- 比较的是他们的元素/成员！！

```python
>>> l = [1, 2, 3, 4, 5]
>>> a = set(l)
>>> b = frozenset(l)
>>> a
{1, 2, 3, 4, 5}
>>> b
frozenset({1, 2, 3, 4, 5})
>>> a == b
True
>>> a in set(b)
False
>>> set(b)
{1, 2, 3, 4, 5}
>>> a in set([b])
True
>>> set([b])
{frozenset({1, 2, 3, 4, 5})}		# 用forzenset作为元素是因为set的元素必须是hashable的数据类型
>>> s = {1,2,3,4,5,6}
>>> a in s                                       # a  in  b  判断的是a 是否是b的元素
False
```

## 不支持frozenset的方法
- Note:
	- frozenset是不可变类型的数据，因此：
		- 虽然上述  &=  |=  -=  等方法对frozenset有效，但是会创建一个新的frozenset
		- 还有下面的这些方法

- set.add(element)
	- add the elemnet to the set

- set.remove(element)
	- remove the element from the set
		- raise keyError if element is not contained in the set.

- set.discard(element)
	-  Remove element elem from the set if it is present.

- set.pop()
	- Remove and return an arbitrary element from the set. Raises KeyError if the set is empty.

- clear()
	- Remove all elements from the set.


 

