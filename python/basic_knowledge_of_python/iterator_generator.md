[点击查看我整理的生成器与迭代器思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/iterator_generator.png?raw=true)

# 迭代器与生成器
## 可迭代对象
>  str、list、tuple、set、dict


- 判断对象是否可迭代
	- in  判断       '\_\_iter__'  in dir(object)
	- `from collections import Iterable  , isinstrance(object, Iterable)`

```python
>>> l = [1,2,3,4,5,6]
>>> __iter__ in dir(l)
Traceback (most recent call last):
  File "<pyshell#5>", line 1, in <module>
    __iter__ in dir(l)
NameError: name '__iter__' is not defined
>>> '__iter__' in dir(l)
True
>>> 

```

- 可迭代标志    对象有\_\_iter__方法     
	- 可迭代协议（可迭代对象成立的必要条件）
		- 对象内部实现了\_\_iter__方法，将一个可迭代对象转换成迭代器
			-  \_\_iter__(self, /)
			    Implement iter(self).
			- for 循环遍历一个可迭代对象，本质上是在迭代过程中，先通过\_\_iter__将iterable对象转换成iterator，然后再调用\_\_next__方法遍历






## 迭代器
- 条件
	- 必须有 \_\_iter__ 方法
	- 必须有 \_\_next__方法
- 判断对象是否是迭代器(Iterator)
	- **不能用is判断**
	-  `from collections import Iterator`


```python
>>> l = map(str, [1,2,3])
>>> from collections import Iterator
>>> l is Iterator         # 不能用is判断
False
>>> isinstance(l,Iterator )
True
>>> 
```

- 迭代器的作用和优点（读取大文件）
	- 可以使用for循环，而for循环就不需要以来索引
	- 节省内存空间
	- 惰性运算
	- 可以用来处理大数据
- 缺点
	- 迭代器中的元素只能使用一次，再此时用需再次迭代

## range()
- 可迭代，但不是迭代器

```python
from collections import Iterable
from collections import Iterator
range_object = range(10)
print('Iterable = = >' , isinstance(range_object, Iterable))
print('Iterator = = >' , isinstance(range_object, Iterator))
-------------------------------------------------------------------------------------
Iterable = = > True
Iterator = = > False

```

```python

l = range(10)
# l_iterator = iter(l)
# from collections import Iterator
# 
# print(isinstance(l_iterator, Iterator))
# print(next(l_iterator))
# print(next(l_iterator))
# print(next(l_iterator))
# print(next(l_iterator))
```

> 不管是一个迭代器还是一个可迭代对象，都是可以用for 循环的。（因为遍历可迭代对象就是先把该对象转换成一个Iterator再进行遍历的）

## 迭代器比可迭代对象多了什么方法？

```python
l = [1, 2, 3]
l_iterator = iter(l)
print(set(dir(l_iterator))  - set(dir(l)))
from collections import Iterable
print('l ==>' , isinstance(l, Iterable))
print('l_Iterator ==''>', isinstance(l_iterator, Iterator))
-----------------------------------------------------------------------

{'__setstate__', '__length_hint__', '__next__'}
l ==> True
l_Iterator ==> True
```

- \_\_next__()
	- Return the next item from the iterator. When exhausted, raise StopIteration
- \_\_length_hint__()
	- 获取迭代器中元素的长度
- \_\_setstate__(index)
	- 根据索引值指定从哪里开始迭代


```python
l = [0, 1, 2, 3, 4, 5, 6]
l_iter = l.__iter__()
# l = iter([0, 1, 2, 3, 4, 5, 6])
print(l_iter)
print(l_iter.__next__())
print(l_iter.__next__())
l_iter.__setstate__(4)
print(l_iter.__next__())
print(l_iter.__next__())

---------------------------------------------------------
<list_iterator object at 0x00000190960694A8>
0
1
4
5
```

## yield关键字
- 也可以向函数返回指定值
- 保存函数暂停与继续运行的状态

- yield与生成器
	- 生成器通过yield关键字来生产数据，如果一个函数内部出现了一个yield关键字，那么这个函数就变成了一个生成器函数。即使这个时候这个函数包含了return关键字，那么它仍然是一个生成器
	- next()调用一次生成器，生成器会下一个返回值返回
- 发生了什么？
	1. 生成器函数执行到yield语句，生成器函数状态被冻结，所有变量值都被保存。
	2. 下一条要执行的语句被记录下来，直到下一次的next（）调用来临
	3. 一旦遇到了next()调用，生成器函数就被激活，运行至下一个yield语句再次被冻结

- 当生成器函数调用了return语句或者next()完了所有yield语句，就会抛出StopIteration异常

## 处理无限序列（或未知长度序列）
- 使用while可以保证生成器源源不断产生新的值，保证生成器不会到达最后

## 生成器Generator
- 本质
	- 迭代器（不需要开发者实现，自带了 
	- \_\_iter__和\_\_next__方法）
	- 由开发者自定义的迭代器

- 生成器函数
	- 一个包含yield关键字的函数就是一个生成器函数。
	- yield的作用
		- 从生成器函数中返回值（但是与return不同）
		- next()方法调用生成器时（或生成器对象调用\_\_next__方法时），会从上一次yield返回的位置向下继续运行到下一个yield


- 生成器实现的方式
	1.  生成器函数
		- 带yield函数，调用生成器函数的时候不执行任何函数中的代码，而是返回一个生成器。调用next的时候才会得到生成器函数的yield返回值，遇到yield时停止，并返回yield的内容
	2. 生成器表达式
		- `geberator = (f(x) for x in sequence if cond(x) ) ` 

- **生成器第一次触发只能用next()或send(None)**
	- 不能是send()，不许指定None


```python

def func():
    print('yield 1111')
    a = 1
    yield a
    print('yield 22222')
    b = 2
    yield b
错误调用方式一（每次都是重新调用新的生成器函数）
print(next(func()))
print(next(func()))
print(next(func()))

正确调用方式
ret = func()
print(ret)
print(ret)
print(ret)
print(ret)

```

# iter()、\_\_iter__
- 相同点：
	- 都是将一个对象转换成iterator
	- 都不改变被转换的原对象


- 区别：
	- 一个是对象方法，一个是内置函数


```python

l = [1,2,3,4,5,6]
l_1 = l.__iter__()
print(l_1)
l_2 = iter(l)
print(l_2)
print(l)

```





## next()与send()
- next()  
	- 激活生成器
		- 也可以用   a.send(None)，必须要有None     
	- 获取下一个迭代值


- send(value)
	- 将value传递给下一个yield。
	- 向生成器传递值
	- **执行一次next()，并返回yield的返回值**
	- 一个要用send方法的生成器函数中至少有两个yield

```python
def generator_send():
    print('send(None)也可以用来激活生成函数，作用是和next()一样的')
    a = yield 'haha'
    print('send(value)，将value传递给上一个yield，然后将值传递给等式左边的值')
    yield a+ 'ziawang'
    yield '没有参数'

s = generator_send()
print(s.send(None))
print(s.send('how old are you ? '))
print(next(s))

-----------------------------------------------------------------
send(None)也可以用来激活生成函数，作用是和next()一样的
haha
send(value)，将value传递给上一个yield，然后将值传递给等式左边的值
how old are you ? ziawang
没有参数

```

## Python中的协程
- 大致思想：	
	- 用yield来暂停执行当前执行的程序，转而执行另一个，在恰当的时候（send）回来执行。
	- 第三方库——greenlet

```python



```





## yield from 
场景1

```python
# 先看这种
# def yield_from():
#     for i in ['how old are you ? ',   'ziawang']:
#         yield i
# 进阶版
def yield_from():
    yield from ['how old are you ?',   'ziawang']

s = yield_from()
print(next(s))
print(next(s))

----------------------------------------------
how old are you ? 
ziawang

```

场景2

```python
def yield_from():
    yield from [i for i in range(10)]

s = yield_from()
while True:
    try:
        print(next(s))
    except StopIteration:
        print('迭代完啦哈哈哈')
        break

```


## 生成器函数调用注意

```python
def func():
    print('yield 1111')
    a = 1
    yield a
    print('yield 22222')
    b = 2
    yield b
# 错误调用方式一（每次都是重新调用新的生成器函数）
# print(next(func()))
# print(next(func()))
#
# 正确调用方式
ret = func()
print(next(ret))
print(next(ret))

--------------------------------------------------
yield 1111
1
yield 22222
2


```




## 各种推导式（解析式）
- 列表推导式
	- list = [f(x) for x in sequence if cond(x)]
- 字典推导式
	- 使用同一个变量：  dic = {k(x):v(x) for x in sequence if cond(x)}
	- 使用不同变量： dic = {key(i):v(j) for i in seq_a for j in seq_b if cond(i, j)}

- 集合推导式
	- set = {f(x) for x in sequence if cond(x)}













