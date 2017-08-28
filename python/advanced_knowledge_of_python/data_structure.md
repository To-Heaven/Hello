> uniform		一致的  

## built-in  sequence
- container sequences  
	- can hold items from different types
		- list, tuple, deque

- flat sequences
	- hold just items of one type
		- str, bytes,  bytearray, memory-view,  array.array

> distinct       确切的，明显的  
> compact     简化，压紧  

- container sequences and flat sequences
	- Container sequences hold references to the objects they contain, which may be of any type
	-  flat sequences physically store the value of each item within its own memory space, and not as distinct objects
		-   flat sequences are more compact, but they are limited to holding primitive values like characters, bytes, and numbers

> Mutable sequences  :
>                  list, bytearray, array.array, collections.deque, and memoryview  


> Immutable sequences :  
>                  tuple, str, and bytes

> trait   特征                    
> extrapolate   推断     
> claim    关点
> explicit    明确的 
> loop  循环    
> scan   扫描  
> abuse   滥用
> incomprehensible      无法理解的
> span        跨度


## list comprehensions and generator expressions
- rules for listcomps
	- If you are not doing something with the produced list, you should not use that syntax
	- try to keep it short
	- If the list comprehension spans more than two lines, it is probably best to break it apart or rewrite as a plain old for loop

> constitution     继续，连续，持续  
> leak        泄露  
> asign       指定，赋值
> consequences      后果  


- Note:
	- In Python code, line breaks are ignored inside pairs of [], {}, or (). So you can build multiline lists, listcomps, genexps, dictionaries and the like without using the ugly \ line continuation escape.
	- Listcomps do everything the map and filter functions do, without the contortions of the functionally challenged Python lambda

> sibling       兄弟姐妹    
> local       局部的  
> scope     范围  


- Listcomps No Longer Leak Their Variables
	- List comprehensions, generator expressions, and their siblings set and dict comprehensions now have their own local scope, like functions
	-  Variables assigned within the expression are local, but variables in the surrounding scope can still be referenced.

```python
C:\Users\Zia Wang>python2
Python 2.7.13 (v2.7.13:a06454b1afa1, Dec 17 2016, 20:53:40) [MSC v.1500 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> x = 'ziawang'
>>> l = [x for x in x]
>>> x
'g'
>>> exit()

C:\Users\Zia Wang>python3
Python 3.6.0 (v3.6.0:41df79263a11, Dec 23 2016, 08:06:12) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> x = 'ziawang'
>>> l = [x for x in x]
>>> x
'ziawang'
>>>

# 注意，在python中，for循环遍历，变量是被保留的

>>> l = []
>>> for i in range(10):
	l.append(i)

	
>>> i
9
>>> l_new = [j for j in range(10)]
>>> j
Traceback (most recent call last):
  File "<pyshell#9>", line 1, in <module>
    j
NameError: name 'j' is not defined
>>> 
```

## Cartesian Products
- a list containing tuples built from all items from two or more lists.
	-The resulting list has a length equal to the lengths of the input iterables multiplied

```python
>>> l = [1, 2]
>>> s = ['a', 'b', 'c', 'd']
>>> res = [(i, j) for i in l for j in s]
>>> res
[(1, 'a'), (1, 'b'), (1, 'c'), (1, 'd'), (2, 'a'), (2, 'b'), (2, 'c'), (2, 'd')]
>>> res_other = [(i, j)for j in s for i in l]
>>> res_other
[(1, 'a'), (2, 'a'), (1, 'b'), (2, 'b'), (1, 'c'), (2, 'c'), (1, 'd'), (2, 'd')]
>>> 
```

> protocol      协议  
> feed      向...提供 
> enclosing parentheses     圆括号
> storage        储存、储藏、仓库
> roster  花名册   
> contrast    对比  


## Generator Expressions
-  a genexp saves memory because it yields items one by one using the iterator protocol instead of building a whole list just to feed another constructor. 
-  If the generator expression is the single argument in a function call, there is no
need to duplicate the enclosing parentheses
	- 注：当生成器不是作为函数的单个参数的时候，就得用圆括号把它和其他参数区分开来了

```python
>>> l = list(str(i) for i in range(10))
>>> l
['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
>>> 

```

> expanse    花费  

- the generator expression feeds the for loop producing one item at a time 
	- user the generator can save the expanse of building a list with a millon items just to feed the for loop

> introductory    引导，简介  
> overlooked       被忽视的  
> vital  重要的    

## use tuples as records   ----- records with no field names
- each item in the tuple holds the data for one field and the position of the item gives its meaning
	- The % formatting operator understands tuples and treats each item as a separate field


```python
>>> traveler_ids = [('USA', '31195855'), ('BRA', 'CE342567'), ('ESP', 'XDA205856')]
>>> for country, _ in traveler_ids:
	print(country)

	
USA
BRA
ESP
>>> daily_work = [('6:30', 'get up'), ('7:30', 'eat breakfirst'), ('12:00', 'have lunch'), ('18:00', 'have dinner')]
>>> for item in sorted(daily_work):
	print('%s  ==>  %s'%item)

	
12:00  ==>  have lunch
18:00  ==>  have dinner
6:30  ==>  get up
7:30  ==>  eat breakfirst
>>> 

```

> dummy  虚设的，假的  
> capture   捕获
> excess     额外的



- for loop knows how to retrieve the items of a tuple separately -----unpacking
	- while in for loop, the element in the items which is not insterested ,assigned to _, a dummy variable.


## tuple unpacking
- Tuple unpacking works with any iterable object. The only requirement is that the iterable yields exactly one item per variable in the receiving tuple, unless you use a star (*) to capture excess items 

> assign     指定，分配  
> application    应用  
> elegant    优雅地   
> temporary   临时的  

- swapping the values of variables without using a temporary variable

```python
a, b = b, a
```


> parallel 平行的  

-  prefixing an argument with a star when calling a function
-  assigning items from an iterable to a tuple of variables  -------   parallel assignment
- Sometimes when we only care about certain parts of a tuple when unpacking, a dummy variable like _ is used as placeholder


```python
import os
_ , filename = os.path.split('\a\b\c.py')
print(filename)

```

> grab   抢占  
> nested  嵌套的  

- the  way of focusing on just some of the items when unpacking a tuple is to use the *
- *args to grab arbitrary excess arguments

```python


```


  
-  In the context of parallel assignment, the * prefix can be applied to exactly one variable, but it can appear in any position

```python


```



> in  addition to   除了...之外

## namedtuple
- Instances of a class that you build with namedtuple take exactly the same amount of memory as tuples because the field names are stored in the class. 
- They use less memory than a regular object because they don’t store attributes in a per-instance __dict__.
- access the fields by name or position.
- Data must be passed as positional arguments to the constructor
- Note:
	- A named tuple type has a few attributes in addition to those inherited from tuple


## tuple  and list
- tuple supports all list methods that do not involve adding or removing items
- tuple lacks the \_\_reversed\_\_ method. 
	-  reversed(my_tuple) works  without it




> exclude    不包括    

##  advanced  form  of  slicing
- why slices and ranges exclude the last item
	-  easy to see the length of a slice or range
	-   easy to compute the length of a slice or range
		-   end - start
	-  It’s easy to split a sequence in two parts at any index without overlapping
