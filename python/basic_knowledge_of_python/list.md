## sequence comparison

> additional		补充的
> custom 		定制的
> comparison		比较
> guarantee		保证

- sequence of the same type support comparision.
- Compared by comparing corresponding elements.
- To compare equal, every element must equal and the two sequences must be of the same type and have the same length.

## 'in' and 'not in'
- support types
	- list
	- tuple
	- str
	- bytes
	- bytearray
 
## list
- 存储多个元素，并且可以修改（应用）


> 列表的长度和列表的元素是可以变化的


- 列表的元素不必是同一类型

- 可以用 +  加号来连接列表

```python
>>> l = [1, 2, 3]
>>> nl = l + [4, 5, 6]
>>> nl
[1, 2, 3, 4, 5, 6]
```

- 类似字符串，列表同样适用切片和索引操作。但是要注意的是，列表切片操作返回的值是一个新列表。
> python中的有序序列都支持切片

```python
>>> l[0:]
[1, 2, 3]
>>> l[:-1]
[1, 2]
>>> l[:0]
[]

```

## 详解切片格式
- list[start : end : step]
	- start 是切片起点索引
	- end是切片终点索引
	- step是步长，默认为1
	- **切片结果中不包含终点索引的值**
	- 在step的符号一致的情况下，start和end可以会和使用正想和反向索引。不管使用什么类型的索引，都要保证start索引位置到end索引位置的方向是和step的方向是一致的。**否则会切出空列表。**
		- 当step为正值的时候，要保持一致，start-->end应该是在列表中从左到右
		- 当step值为负数的时候，要保持一致，start-->end应该是在列表中从右往左

```python
>>> l = ['A', 'B', 'C', 'D']
>>> l[-4:2]
['A', 'B']
>>> l[3:-3:-1]
['D', 'C']
>>> 
```

> step的正负决定了切片结果的元素采集的先后

```python
>>> l[::-1]
['D', 'C', 'B', 'A']
>>> l[::]
['A', 'B', 'C', 'D']
>>> l[:]
['A', 'B', 'C', 'D']
>>> 
```

- list是可以修改的
	- 修改方式一：通过索引修改元素值
	- 修改方式二：通过对切片赋值来批量修改列表中的元素

```python
>>> l = ['a', 'b', 'c', 'd', 'e', 'f']
>>> l[0] = 1
>>> l
[1, 'b', 'c', 'd', 'e', 'f']
>>> l[-6:4]
[1, 'b', 'c', 'd']
>>> l[-6:4] = []
>>> l
['e', 'f']
>>> 
```

## get list
- lists can be constructed in several ways:
	- a pair of square brackets to denote a empty list    []
	- ['a',' b',' c']
	- list comprehension  [f(x) for x in iterable ]
	- list(iterbale)
	- list(iterator)
   


## list method
- list.append(sub)  在列表的末尾添加新元素（新元素可以是任何数据类型）

>相当于  list[len(list):] = sub


```python
l.append(555)
>>> l
['A', 'B', 'C', 'D', 555]
>>> l
['A', 'B', 'C', 'D', 555]
>>> l.append((1,2,3))
>>> l
['A', 'B', 'C', 'D', 555, (1, 2, 3)]
>>> l.append([1,2,3])
>>> l
['A', 'B', 'C', 'D', 555, (1, 2, 3), [1, 2, 3]]
>>> l.append({1:'one'})
>>> l
['A', 'B', 'C', 'D', 555, (1, 2, 3), [1, 2, 3], {1: 'one'}]
>>> 
```

- list.extend(list) 将一个给定列表中的所有元素都添加到另一个列表中
	- **注意：这个方法的返回值为None，无返回值，注意下面的例子**

> 相当于   list[len(list):] = l

```python
>>> list = [1, 2, 3]
>>> l = [4, 5, 6]
>>> list.extend(l)
>>> list
[1, 2, 3, 4, 5, 6]
>>> 

>>> l = []
>>> s = [1,2,3]
>>> l = l.extend(s)
>>> print(l)
None
>>> 
```

- list.insert(index, sub) 在指定索引index位置插入元素sub，元素的数据类型可以是多种数据类型

```python
>>> list
[1, 2, 3, 4, 5, 6]
>>> list.insert(1, ('可以插入','元组'))
>>> list
[1, ('可以插入', '元组'), 2, 3, 4, 5, 6]
>>> list.insert(1, {1:'1', 2:'2'})
>>> list
[1, {1: '1', 2: '2'}, ('可以插入', '元组'), 2, 3, 4, 5, 6]
```

- list.remove(sub) 删除列表中值为sub的第一个元素，如果没有这个元素就会返回一个错误
> 单纯的删除，没有返回值，即None


```python
>>> list.remove({1:'1', 2:'2'})
>>> list
[1, ('可以插入', '元组'), 2, 3, 4, 5, 6]
>>> list.remove('haha')
Traceback (most recent call last):
  File "<pyshell#11>", line 1, in <module>
    list.remove('haha')
ValueError: list.remove(x): x not in list
>>> 

```

- list.pop([index]) 删除指定索引index位置的元素，并返回该元素，如果index没有被指定，那么会返回最后一个元素的值，返回该值后，元素立即从列表中删除
> 有返回值，与remove和del 不同。**应用：一边删除，一边用变量接收返回的值（即从列表中取值）**

```python
>>> list
[1, ('可以插入', '元组'), 2, 3, 4, 5, 6]
>>> list.pop()
6
>>> list.pop(1)
('可以插入', '元组')
>>> list
[1, 2, 3, 4, 5]
>>> 

```

- list.clear() 无返回值，清除列表中的所有元素，但不会删除该列表

```python
>>> list.clear()
>>> list
[]
>>> 

```

- list.index(sub) 返回值为列表中值为sub的第一个元素的索引
	

- list.count(sub) 返回值为sub在list中出现的次数


- list.sort(*, key=None, reverse=False) 无返回值，对列表中的元素进行排序
	- the two arguments must be called by keyword
	- key ---> the function name 

- list.reverse()无返回值，将列表中的元素倒序排列

```python
>>> list = [1,5,4,6,8,7,2,1,2,4,5,5,7,8,9,5,2,2,1,2,1]
>>> list.count(1)
4
>>> list.index(1)
0
>>> list.reverse()
>>> list
[1, 2, 1, 2, 2, 5, 9, 8, 7, 5, 5, 4, 2, 1, 2, 7, 8, 6, 4, 5, 1]
>>> list.sort()
>>> list
[1, 1, 1, 1, 2, 2, 2, 2, 2, 4, 4, 5, 5, 5, 5, 6, 7, 7, 8, 8, 9]
>>> print(list.sort())
None
>>> print(list.reverse)
<built-in method reverse of list object at 0x000001E84E34C388>
>>> print(list.reverse())
None
>>> print(list.clear())          ## 注意，虽然这一步将list.clear()放在了print中，但是list中的元素仍然被清理了
None
>>> list
[]
>>> 

```

- list.copy()  返回一个浅拷贝，浅拷贝指创建一个同样指向该list的变量，但是不会在内存中创建另一个相同内容的list   

>  效果同  list[:]


## 把列表当作堆栈使用(先进后出，后进先出)
- 方法
	- list.append()
	- list.extend()
	- list.pop([])

```python
>>> list
[1, 2, 3]
>>> list.append(4)
>>> list.append(5)
>>> list.pop()
5
>>> list.pop()
4
>>> list
[1, 2, 3]
```

## 把列表当作队列使用（先进先出，后进后出）
- 队列最先进入的元素最先释放，但是这样用的效率不高
- 相对来说，从列表末尾田家河弹出很快，在头部插入和弹出很慢（因为为了一个元素压迫移动整个列表的所有元素）
- 实现队列的条件
	- **collections.deque**


```python
>>> q = deque(['ziawang', 23, 'haha'])
>>> q.append('hello !')
>>> q.append('么么哒')
>>> q
deque(['ziawang', 23, 'haha', 'hello !', '么么哒'])
>>> q.popleft()
'ziawang'
>>> q.popleft()
23
>>> q
deque(['haha', 'hello !', '么么哒'])
>>> 
```

## 列表推导式——从序列中创建列表
- 在推导列表推导式之前，普通方法是将序列中的元素迭代，通过返回的元素创建列表。
- 列表最大的优势就是简单明了的

```python
l = [{'name':'ziawang'}, {'name':'egon'}]

l_other = [i['name'] for i in l]
print(l_other)
```

```python
>>> l = []
>>> for i in range(1, 20, 3):
	l.append(i)

	
>>> l
[1, 4, 7, 10, 13, 16, 19]
>>> i         #  i  仍然存在
19
>>> 
```

- 列表推导式运行逻辑流程

```python
#    l = [('%s:%s'%(i, j)) for  i in range(1, 3, 1) for j in ['name', 'age', 'city']]
l = []
for i in range(1, 3, 1):
    for j in ['name', 'age', 'city']:
        l.append('%s:%s'%(i, j))

print(l)
print(i)				#  i在内存中仍然存在
```

**但是这种方法是有弊端的**
	- 在for循环中，被创建的  i   变量在循环结束之后时依然存在的。

- **列表推导式**
	- 列表解析就是使用列表生成式生成一个自定义列表的过程，而且性能比利用循环遍历的方法快许多
	- **列表解析**
		- 中括号中的表达式会在for循环每次完成时执行一次。即使两者内容不相关，for循环保证了表达式执行的次数
		
```python
>>> l = ['ok ' for i in range(10)]
>>> l
['ok ', 'ok ', 'ok ', 'ok ', 'ok ', 'ok ', 'ok ', 'ok ', 'ok ', 'ok ']
>>> str_list = ['ziawang', 'is', 'name', 'my']
>>> say_list = [str_list.pop() for i in range(len(str_list))]
>>> say_list
['my', 'name', 'is', 'ziawang']
>>> 
```
		- 
	- 由包含一个表达式的中括号组成，表达式后面跟随一个for子句，之后可以有零个或多个for或if语句来控制输出的列表。
	- PS：如果由小括号来构成的话，那么得到的将是一个生成器generator。
	- 如果想要的得到列表元素为tuple，那么就必须把i和j用括号放在一起，不然会报错，虽然tuple在创建的时候可以不要括号

```python
>>> tup = (i**2 for i in range(10))
>>> tup
<generator object <genexpr> at 0x0000013E82EBA0F8>
>>> next(tup)
0
>>> l = [ i for  i in range(10)]
>>> l
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> l =[(i, j) for i in range(1, 20, 5) for j in [1, 2, 3, 4, 5, 6, 7, 8, 9] if i != j]
>>> l
[(1, 2), (1, 3), (1, 4), (1, 5), (1, 6), (1, 7), (1, 8), (1, 9), (6, 1), (6, 2), (6, 3), (6, 4), (6, 5), (6, 7), (6, 8), (6, 9), (11, 1), (11, 2), (11, 3), (11, 4), (11, 5), (11, 6), (11, 7), (11, 8), (11, 9), (16, 1), (16, 2), (16, 3), (16, 4), (16, 5), (16, 6), (16, 7), (16, 8), (16, 9)]
>>> l =[ i,j for i in range(1, 20, 5) for j in [1, 2, 3, 4, 5, 6, 7, 8, 9] if i != j]
SyntaxError: invalid syntax


>>> [i:j  for i in range(5) for j in [7,8,9,4,5]]
SyntaxError: invalid syntax
>>> [(i:j)  for i in range(5) for j in [7,8,9,4,5]]
SyntaxError: invalid syntax
>>> [('%s:%s')%(i, j)  for i in range(5) for j in [7,8,9,4,5]]
['0:7', '0:8', '0:9', '0:4', '0:5', '1:7', '1:8', '1:9', '1:4', '1:5', '2:7', '2:8', '2:9', '2:4', '2:5', '3:7', '3:8', '3:9', '3:4', '3:5', '4:7', '4:8', '4:9', '4:4', '4:5']
>>> 
```
 
	- 列表生成式可以用更复杂的表达式和嵌套函数来创造想要的列表

```python
>>> from math import pi
>>> [str(round(pi, i)) for i in range(1, 6)]
['3.1', '3.14', '3.142', '3.1416', '3.14159']
```


	- **同样可以生成嵌套的列表**



```python
>>> matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
>>> [[row[i] for row in matrix] for i in range(4)]
[[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
>>>
```	


> 等价于

```python
matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]

l = []
for i in range(4):
    l_row = []
    for row in matrix:
        l_row.append(row[i])
    l.append(l_row)

print(l)
```


> 或者


```python
matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]

l = []
for i in range(4):
    l.append([row[i] for row in matrix])

print(l)
```


## 无穷嵌套列表(还有无穷嵌套字典)
- a.append(a)

```python
>>> a = [1,2,3,4]
>>> a.append(a)
>>> a
[1, 2, 3, 4, [...]]
>>> a[3]
4
>>> a[4]
[1, 2, 3, 4, [...]]
>>> a[4][4][4][4][4][4][4][4][4][4][4][4] == a
True
>>> 

```

## 列表重构

```python
>>> l = [1, 2, 3, [4, 5, 6], 7, [8, 9]]
>>> sum(l, [])
Traceback (most recent call last):
  File "<pyshell#14>", line 1, in <module>
    sum(l, [])
TypeError: can only concatenate list (not "int") to list
>>> l = [[1, 2, 3], [4, 5], [6], [7, 8, 9]]
>>> sum(l, [])
[1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> 
```









