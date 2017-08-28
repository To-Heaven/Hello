## 单词
interaction	交互			credential		证件    
metaphor	比喻，象征		scenario			脚本    
illustrate		说明			replica			复制品  
sticky		便利贴			distinct			明显的，有区别的  
delibrate 	深思熟虑的		singleton		单例值  
assignment	分配			recommend		被推荐的  
exception	异常			negation			反面  
proof		证明			invoke			调用  
instantiaced	实例化的		inherit			继承  
retrieved		恢复			implemention		方法  
label		标签			extend			延伸  
aliase		别名			inspect			检查  
operator		操作符			
confirm		证明			dumplicated		复制  
suppose		猜想			depcit			描述  
imposter		冒充者			arbitrary			任意  

## variables are not boxes
- variables a and b hold references to the same list, not copies of the list
	
```python
>>> l_a = [1, 2, 3]
>>> l_b = l_a
>>> l_a.append(4)
>>> l_a
[1, 2, 3, 4]
>>> l_b
[1, 2, 3, 4]
>>> 

```

- object is created before the assignment, so the variable is assigned to the object , **never** the object assigned to the variable（对象在赋值给变量之前就已经创建了，因此要说把变量分配给对象，而不是把对象分配给变量）
- variables are asigned to objects only after the objects are created

```python
>>> class F:
	def __init__(self):
		print('create object F()  id = %s'%id(self))

		
>>> a = F()
create object F()  id = 2309281658920
>>> b = F() * 3
create object F()  id = 2309281009792
Traceback (most recent call last):
  File "<pyshell#17>", line 1, in <module>
    b = F() * 3
TypeError: unsupported operand type(s) for *: 'F' and 'int'
>>> 
```

## identity,  equality and aliases
-   the  '  is  '   operator compares the identity of two objects
	-   id() function returns an integer representing its identity
-   id() return the memory adress of the object , but ** it may be something else in another python interpreter**.
-   the ID is  a unique numeric label, it will never change duiring the life of the object 
-   **identity checks are most often done with the  ' is  ' operator, not boy the IDs**

```python
>>> a = [1, 2, 3, 4, 5, 6]
>>> b = a
>>> id(a)
2309281544008
>>> id(b)
2309281544008
>>> a is b
True
>>> c = [1, 2, 3, 4, 5, 6]
>>> c is a
False
>>> id (c)
2309281355400
>>> 
```

## ' is '  and  ' == '
- ' is'  operator compares the identity of two objects
	- the most common case is checking whether a variable is bound to '  None  '
- ' == 'object compares the values of objects, value is the data they hold, not the identitied
	- ' a == b ' is syntactic sugar for a.__eq__(b)
	-   The __eq__ method inherited from object compares object IDs



> contiguous		临近的    

## the relative immutability of tuples
- the immutablility of tuples really refers to the physical contents of the tuple data  structure ---the references it holds . and does not extend the referenced objects
- single_type sequences like str, bytes, and array.array are flat : 
	- they **don't contain references but physically hold their data** -- characters, bytes, and numbers --- in contiguous memory


```python
>>> t = (1, 2, [3, 4])
>>> id(t)
1783165222984
>>> id(t[2])
1783165203784
>>> t[2].append(5)
>>> t
(1, 2, [3, 4, 5])
>>> id(t)
1783165222984
>>> id(t[2])
1783165203784


>>> l = [1, 2, [3, 4]]
>>> id(l)
1783164967240
>>> l[2].append(5)
>>> id(l[2])
1783123406664
>>> l[2].append(6)
>>> l
[1, 2, [3, 4, 5, 6]]
>>> id(l[2])
1783123406664
>>> l[0] = 0
>>> l
[0, 2, [3, 4, 5, 6]]
>>> id(l)
1783164967240
>>> 
```



## copys are shallow by default
- how to get a shallow copy
	1. use the builtin contructor
	2. use the [:] , the object is a sequence

```python
>>> t = [1, 2, [3, 4]]
>>> s = t
>>> s is t
True
>>> m = list(t)
>>> m is t
False
>>> 
```

other examples:

```python
>>> l = [1,2,3,4,5,6]
>>> s = list(l)
>>> m = l[:]
>>> l is s
False
>>> m is s
False
>>> id(m)
2148643958600
>>> id(l)
2148685821448
>>> id(s)
2148685284552
>>> m == l == s
True
>>> 

``` 

-  shallow copy
	-  the outermost cntainer is duplicated, but the copy is filled with reference to the same items held by the original container（作者是说：浅拷贝复制了最外层的容器，得到的副本中的元素仍然是对源容器对象的引用）
	-   if all the items are immutable , this saves memory and causes no problems
	-   if there are mutable items, this may affect the referenced objects

```python
>>> l = [1, 2, 3, [4, 5], (6, 7)]
>>> l_shallow = l.copy()
>>> l_shallow
[1, 2, 3, [4, 5], (6, 7)]
>>> l[3][0] = 0
>>> l
[1, 2, 3, [0, 5], (6, 7)]
>>> l_shallow
[1, 2, 3, [0, 5], (6, 7)]
>>> 

```


	-   **shallow copy is easy to get , but they may not be what we want**

```python
>>> l = [1, 2, [3, 4]]
>>> t = list(l)
>>> t is l
False
>>> id(t[-1]) == id(l[-1])
True
>>> l[-1].append(5)
>>> l
[1, 2, [3, 4, 5]]
>>> t
[1, 2, [3, 4, 5]]
>>> l.append('sss')
>>> l
[1, 2, [3, 4, 5], 'sss']
>>> t
[1, 2, [3, 4, 5]]
>>> 

```

> embeded		深入的  
> scheme			设计  

## deepcopy
- deepcopy
	- dumplicates that do not shares references of embeded objects

```python
import copy

class Bus:
    def __init__(self, passengers = None):
        if passengers == None:
            self.passengers = []
        else:
            self.passengers = passengers


    def pick(self, name):
        self.passengers.append(name)

    def drop(self, name):
        self.passengers.remove(name)


bus1 = Bus(['laosiji', 'ziawang', 'zhangsan', 'lisi'])
bus2 = copy.copy(bus1)
bus3 = copy.deepcopy(bus1)
print('''
bus1: %s
bus2: %s
bus3: %s  '''%(id(bus1), id(bus2), id(bus3)))
print('''
bus1.passengers: %s
bus2.passengers: %s
bus3.passengers: %s  '''%(id(bus1.passengers), id(bus2.passengers), id(bus3.passengers)))

# --------do something ---------haha------------

bus1.drop('lisi')
print('bus1:', bus1.passengers)
print('bus2:', bus2.passengers)
print('bus3:', bus3.passengers)

------------------------------------------------------------------------------------------------------
# 结果
bus1: 1607599110520
bus2: 1607567033232
bus3: 1607599264712  

bus1.passengers: 1607600475528
bus2.passengers: 1607600475528
bus3.passengers: 1607600474312  
bus1: ['laosiji', 'ziawang', 'zhangsan']
bus2: ['laosiji', 'ziawang', 'zhangsan']
bus3: ['laosiji', 'ziawang', 'zhangsan', 'lisi']

```

## function parameters as references
- ' call by sharing  ' is the only mode of parameter passing in python. this means that each formal parmeter of the function get the copy of the** reference** in the arguments. in other words , the parameters inside the function become aliases of the actual arguments（formal parameter形参， actual argument实参）
- **the result of this scheme is that a function may change any mutable object passed as a parameter, but it cannot change the identity of those objects**

```python
定义函数，分别传入不同变量相加，查看实参的变化

```

```python
列表作为__init__参数
```
