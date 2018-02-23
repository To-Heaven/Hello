[点击查看我整理的生成器与迭代器思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/iterator_generator.png?raw=true)

## 可迭代对象
#### 可迭代对象是什么鬼？
- 可迭代标志：对象有`__iter__`方法     
	- 可迭代协议（可迭代对象成立的必要条件）
		- 对象内部实现了`__iter__`方法，当for循环遍历一个可迭代对象时，会将这个可迭代对象转换成迭代器，然后调用这个可迭代对象的`__next__`方法返回迭代器中的一个个元素
			-  比如`__iter__(self, value); return iter(value)`


#### 判断对象是否可迭代
- `in`进行成员判断
	- `'__iter__'  in dir(iter_obj)`
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

#### `__iter__`方法举例
- 举一个非常简单的例子，我们使用`__iter__`创建一个可迭代对象，但后遍历该对象

```python
class MyIter(object):
    def __init__(self, value):
        self.value = value

    def __iter__(self):
        """ 当遍历实例对象的时候，就会执行此方法

        """
        yield from self.value


myiter_obj = MyIter('ziawang')
for i in myiter_obj:
    print(i)		
```


## 迭代器
#### 条件
1. 必须有 `__iter__` 方法
2. 必须有 `__next__`方法

#### 判断对象是否是迭代器(Iterator)
- **不能用is判断**
	- `is`比较的是两个对象的id地址，比较的两者不是一个对象，Iterator是一个类对象。
- 使用`in`成员运算符，不过要比判断可迭代对象多一个条件
	- `if "__iter__" in dir(obj) and "__next__" in dir(obj)`
-  `from collections import Iterator`配合`isinstance`判断


```python
>>> l = map(str, [1,2,3])
>>> from collections import Iterator
>>> l is Iterator         # 不能用is判断
False
>>> isinstance(l,Iterator )
True
>>> 
```

##### 迭代器的作用和优点（读取大文件）
- 可以使用for循环，而for循环就不需要以来索引
- 节省内存空间
- 惰性运算
- 可以用来处理大数据

##### 缺点
- 迭代器中的元素只能使用一次，再次使用需要再次迭代
	- 其实迭代内部有一个`__setstate__(index)`方法可以让迭代器从指定位置重新迭代

##### range()
- 可迭代，但不是迭代器

```python
from collections import Iterable
from collections import Iterator
range_object = range(10)
print('Iterable = = >' , isinstance(range_object, Iterable))
print('Iterator = = >' , isinstance(range_object, Iterator))
#-------------------------------------------------------------------------------------
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

#### 迭代器比可迭代对象多了什么方法？

```python
l = [1, 2, 3]
l_iterator = iter(l)
print(set(dir(l_iterator))  - set(dir(l)))
from collections import Iterable
print('l ==>' , isinstance(l, Iterable))
print('l_Iterator ==''>', isinstance(l_iterator, Iterator))
# -----------------------------------------------------------------------

{'__setstate__', '__length_hint__', '__next__'}
l ==> True
l_Iterator ==> True
```

- `__next__`
	- Return the next item from the iterator. When exhausted, raise StopIteration
- `__length_hint__`
	- 获取迭代器中元素的长度
- `__setstate__(index)`
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

# ---------------------------------------------------------
<list_iterator object at 0x00000190960694A8>
0
1
4
5
```



#### 处理无限序列（或未知长度序列）
- 使用while可以保证迭代器源源不断产生新的值，保证迭代器不会到达最后

## 生成器Generator
- 本质
	- 迭代器（不需要开发者实现，自带了 `__iter__`和`__next__`方法）

#### yield关键字
- 向函数返回指定值
- 保存函数暂停与继续运行的状态


#### yield与生成器
- 生成器通过yield关键字来生产数据，**如果一个函数内部出现了一个yield关键字，那么这个函数就变成了一个生成器函数。即使这个时候这个函数包含了return关键字，那么它仍然是一个生成器**
- next(gene_obj)每调用一次生成器，生成器会下一个返回值返回
	- 发生了什么？
		1. 生成器函数执行到yield语句，生成器函数状态被冻结，所有变量值都被保存。
		2. 下一条要执行的语句被记录下来，直到下一次的`next（）`调用来临
		3. 一旦遇到了`next()`调用，生成器函数就被激活，运行至下一个yield语句再次被冻结

- 当生成器函数调用了`return`或者`next()`执行完了所有`yield`语句，就会抛出`StopIteration`异常

#### 生成器函数
- 一个包含yield关键字的函数就是一个生成器函数。
- yield的作用
	- 从生成器函数中返回值（但是与return不同）
	- next()方法调用生成器时（或生成器对象调用\_\_next__方法时），会从上一次yield返回的位置向下继续运行到下一个yield


#### 生成器实现的方式
1.  生成器函数
	- 带yield函数，**调用生成器函数的时候不执行任何函数中的代码，而是返回一个生成器**。**调用next的时候才会得到生成器函数的yield返回值，遇到yield时停止，并返回yield的内容**
2. 生成器表达式
	- `geberator = (f(x) for x in sequence if cond(x) ) ` 

- **生成器第一次触发只能用`next()`或`send(None)`**
	- 不能是`send()`


```python

def func():
    print('yield 1111')
    a = 1
    yield a
    print('yield 22222')
    b = 2
    yield b
# 错误调用方式一（每次都是重新调用新的生成器函数）
print(next(func()))
print(next(func()))
print(next(func()))

# 正确调用方式
ret = func()
print(ret)
print(ret)
print(ret)
print(ret)

```

#### `iter()`、`__iter__`
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





##### next()与send()
- next()  
	- **激活生成器**
		- 也可以用   `a.send(None)`，必须要有None     
	- 获取下一个迭代值


- send(value)
	- **执行一次next()，并返回yield的返回值**
	- 将value传递给下一个yield。
	- 向生成器传递值
	- 一个要用send方法的生成器函数中至少有两个yield

```python
def g():
    print('开始')
    yield 1
    a = yield 2
    print(a+1)


s = g()
print(s)        # 开始
ret1 = next(s)
print(ret1)      # 1


ret2 = next(s)
print(ret2)        # 每一次执行next，都会返回下一个yield的值
s.send(3)           # 打印出4

```

##### Python中的协程
- 大致思想：	
	- 用yield来暂停执行当前执行的程序，转而执行另一个，在恰当的时候借助`send`回来继续执行原操作。
	- 第三方库——greenlet

```python



```





##### yield from 
场景1

```python
# 先看这种
# def test_yield_from():
#     for i in ['how old are you ? ',   'ziawang']:
#         yield i
# 进阶版
def test_yield_from():
    yield from ['how old are you ?',   'ziawang']

s = test_yield_from()
print(next(s))
print(next(s))

# ----------------------------------------------
how old are you ? 
ziawang

```

场景2

```python
def test_yield_from():
    test_yield from [i for i in range(10)]

s = test_yield_from()
while True:
    try:
        print(next(s))
    except StopIteration:
        print('迭代完啦哈哈哈')
        break

```


##### 生成器函数调用注意

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

# --------------------------------------------------
yield 1111
1
yield 22222
2


```



#### 迭代器与生成器区别
- 迭代器
	- 对现有的数据进行迭代，数据必须是当下存在的
- 生成器
	- 可以生成当下不存在的数据对象，可以看作是`无中生有`



#### 各种推导式（解析式）
- 列表推导式
	- list = [f(x) for x in sequence if cond(x)]
- 字典推导式
	- 使用同一个变量：  `dic = {k(x):v(x) for x in sequence if cond(x)}`
	- 使用不同变量： `dic = {key(i):v(j) for i in seq_a for j in seq_b if cond(i, j)}`
		- 注意有坑，这种生成式要遍历最多`i*j`次，类似笛卡儿积

- 集合推导式
	- `set = {f(x) for x in sequence if cond(x)}`






#### 应用
> Web开发中，对于需要二次加工的数据（后台加工+模板渲染处理），后台传递可以使用yield来给模板传递要加工的数据，从而避免重复遍历

- 问卷调查项目
	1. 使用生成器函数生成每一个问卷中的问题
	2. 如果问题是"选择题"类型，就会使用生成器函数中的`yield`返回每一个选项
- seconds项目
	1. 生成表头时使用生成器函数
	2. 生成批量生成搜索条件







