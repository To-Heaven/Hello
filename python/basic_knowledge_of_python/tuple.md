## tuple
- class tuple(object)
	- tuple() -> empty tuple
	- tuple(iterable) -> tuple initialized from iterable's items  
		- If the argument is a tuple, the return value is the same object.

```python
>>> t = tuple()
>>> t
()
>>> t = 1, 2
>>> t
(1, 2)
>>> t = tuple('biubiubiu')
>>> t
('b', 'i', 'u', 'b', 'i', 'u', 'b', 'i', 'u')
>>> 
```

- 一对空圆括号可以创建一个空元组
- 一个单元素后面加一个逗号可以创建单元素的元组，虽然长得丑，但是很有效

```python
>>> tup = 1, 2, 3, 4, 5, 6
>>> tup
(1, 2, 3, 4, 5, 6)
>>> tup[6] = 7                                                        #tuple一旦创建就不能再改变其长度
Traceback (most recent call last):
  File "<pyshell#96>", line 1, in <module>
    tup[6] = 7
TypeError: 'tuple' object does not support item assignment
>>> tup_new = tup, ['a', 'b', 'c']                     #get 屌炸天的技能
>>> tup_new         
((1, 2, 3, 4, 5, 6), ['a', 'b', 'c'])
>>> t = ()
>>> t
()
>>> t = (1, )
>>> t
(1,)
>>> 
```

- **不可变的特性决定了元组用作查询的功能**
	- 封装用户信息（修改信息需要重新建立元组）
	- tuple的不可变性在进阶中会着重讲

- 元组由数个逗号分割的值组成，元组可以用圆括号括起来也可以不用。但是当元组是更大的表达式的一部分的时候，就必须使用圆括号
- 元组和字符串一样是不可变的。
	- 不能对其元素进行赋值
	- 不能进行切片
	- 不能为现有元组增加长度

- **元组的元素可以是可变的数据类型**


```python
>>> t = (1,2,3,[1,2,3])
>>> id(t)
1795283958744
>>> id(t[-1])
1795284162312
>>> t[-1] = [0,0,0]
Traceback (most recent call last):
  File "<pyshell#10>", line 1, in <module>
    t[-1] = [0,0,0]
TypeError: 'tuple' object does not support item assignment
>>> t[-1][0] = 0
>>> id(t[-1])
1795284162312
>>> 
```

## 多重赋值
- tuple packing  和 sequence unpacking           （元组封装和序列拆封）
	- 序列拆封的右边可以是任何线性序列，但是左侧变量数目和右侧序列元素数量一致

- 多重赋值的本质就是tuple packing（元组打包）合Sequence unpacking（序列解包）
- 被解压的对象可以是任意序列，但是字典被解压后，得到的是它的key而不是一个key-value键值对
	- 举个栗子

```python
>>> tup = (1, 2, 3)    
>>> a,b,c = tup           #  序列解包，这里tup需要是一个三元的tuple，不必须是元组，也可以是列表，如果t               
                                           不是三元的有序序列，就会抛出ValueError异常
>>> a
1
>>> b
2
>>> c
3
>>> s = a,b,c                   # 元组打包，按照tuple构建的语法，s一定是一个元组，并且元组的元素个数与等式 
                                          右边的个数相同。
>>> s
(1, 2, 3)
>>> 
```
- 更屌的，可以把一个有序序列的元素批量赋值给另一个有序数列的元素。

```python
>>> tup = (q, w, e) = [1,2,3]
>>> tup
[1, 2, 3]
>>> q
1
>>> w
2
>>> e
3
>>> 
```


- tup.count(value)
	- return number of occurrences of value

- tup.index(valua[start[end]])
	- return first index of value.
	- Raises ValueError if the value is not present.























