## 充分利用惰性运算（效率）
- 惰性运算
	- 所谓惰性运算，即仅仅在真正需要执行一段代码的时候才执行该代码，最小化计算机要做的工作
- 惰性运算的好处
	- 提高效率，避免没必要的运算
	- 可以构造一个无限长度的数据结构，节省空间

- 逻辑运算中的惰性运算
	- `if True or False`
		- 只要or左边为True，就直接返回结果，不再计算or后面的表达式的结果
	- `if False and True`
		- 只要and左边是False，就直接返回结果，不再计算and后面的表达式的结果
	- 综上
		- 对于and运算，应将False可能性高的判断内容放在and左边
		- 对于or运算，应将True可能性高的判断内容放在or的左边 
- 生成器
	- [获取生成器与迭代器知识](http://www.ziawang.com/python/basic_knowledge_of_python/iterator_generator.html)

## ++i  与  --i
- python中是支持`++i` 和 `--i` 运算的，但是仅仅是对符号的判断运算
	- 比如`--2`和`++2`两者得到`2`
	- 但是`2++`  `2--` 肯定会报错


## 连接字符串使用join而不是 + 
- 用`+`连接字符串
	- 每一次通过`+`连接两个字符串都会返回一个新的字符串对象，这就意味着在内存空间中就必须创建内存空间用于存放该对象，虽然python有垃圾处理机制，但是当用`+`连接大量的字符串时，每一次两个字符串的拼接都会申请和复制一次内存，频繁的在内存中开辟内存空间以及字符串对象的存取必然会对性能产生非常大的影响。
	
- 用`s.join(iterable)`连接字符串
	-  使用字符串的`join`方法拼接字符串的时候，解释器会首先计算并申请拼接整个字符串所需要的内存空间，然后将字符序列中的每一个元素复制到内存中去，整个过程只创建一次内存空间，大大提高了效率
	-  **join方法作用的对象必须是字符串序列**


## 生成式中要注意的地方
- 字典生成式的坑

```python
list_map = [(key, value) for key in 'abcdefg' for value in range(7)]
dict_iter = dict(list_map)
print(dict_iter)
# ------------------------------------------------------
{'a': 6, 'b': 6, 'c': 6, 'd': 6, 'e': 6, 'f': 6, 'g': 6}

# ------------------------------------------------------
dict_exp = {key:value for key in 'abcde' for value in range(10)}

print(dict_exp)
# --------------------------------------------------------
{'a': 9, 'b': 9, 'c': 9, 'd': 9, 'e': 9}
```



## with上下文管理
- with语句格式

```python
with 表达式  [as  别名]:
	代码块
```

- with语句的作用
	- 在代码块执行完毕或者遇到异常中断执行的情况下，执行清理工作，将上下文环境恢复到代码块执行之前
	- 主要用于
		- file object 的关闭
			- 文件
			- 套接字
			- stream流
			- 进程/线程/进程池/线程池
		- 锁的释放release
	
- 上下文管理的实现

```python
class TestWith:
    def __init__(self, name):
        self.name = name

    def __enter__(self):
        print('__enter__ running . . .', self.name)

    def __exit__(self, exc_type, exc_val, exc_tb):
        print('__exit__ running , , , ', self.name)


a = TestWith('ziawang')
with a :
    import time
    time.sleep(2)
``` 




## 循环中的else和try分支中的else
- 之所以在这里又强调了一次循环中的else，是因为一直以来忽略了循环中else的另一个执行条件
- **除了当循环语句正常执行结束运行else之外，当循环条件不成立的时候，也会触发else语句**

```python

while False:
    print('不会被执行')
else:
    print('循环条件不成立的时候也会执行')
``` 

- 当只有try语句中的代码块正常执行完才能执行其他代码的时候，就用到了else分支，将紧跟者要运行的代码放在else中
	- 简单的说就是，else中的代码块执行的前提条件就是try中的代码块完整执行，否则不能满足执行else中代码块的条件

```python

import pymysql

conn = pymysql.connect(host = 'localhost',
                       user = 'root',
                       password = 'pass',
                       database = 'db',
                       charset = 'utf8',
                       cursorclass = pymysql.cursors.DictCursor)

def insert_into_tb(conn, attr):
    cursor = conn.cursor()
    try:
        cursor.execute('insert into tb VALUES(%s %s)'%attr)
    except Exception:
        print('rollback . . .')
        conn.rollback()
    else:
        conn.commit()			# 只有当实行execute成功才能提交数据，将变化写入内存
    finally:
	cursor.close()
	conn.close()
insert_into_tb(conn, ('ziawang', 22))
```


## 对象的浅拷贝与深拷贝
- 浅拷贝
	- 创建一个新对象，如果被拷贝的目标对象中存在对其他对象的引用，那么浅拷贝会将这些引用也拷贝给新对象。
	- 即，拷贝的是对其他对象的引用
- 深拷贝
	- 创建一个新对象，如果被拷贝的目标对象中存在对其他对象的引用，那么深拷贝会将引用指向的对象也拷贝一份保存在新对象中。
	- 这样通过深拷贝创建的对象内就不包含其他对象的引用，对其他对象的操作就不会影响到深拷贝得到的对象



## 迭代器协议
- 迭代器中只是提到了迭代协议的内容，并没有深究，这里将之前的总结一下，再继续深入
	- 可迭代对象
		- 可迭代对象即对象具备`__iter__()`方法，对象调用该方法后返回一个迭代器
		- for 循环遍历可迭代对象实际上就是使对象调用自身的`__iter__()`方法成为迭代器之后，再一个个将容器内的元素迭代
	- 迭代器协议有两段内容
		1.  对象实现了`__iter__()`方法
		2.  对象实现了`__next__()`方法
			- `__next__()`方法会返回当前指向的元素，并指向下一个元素的位置，当再次调用时，返回指向的元素，并指向再下一个元素。当当前没有再指向元素时，就抛出StopIteration异常
-  for 循环封装的功能
	-  获取容器对应的的迭代器(对象调用`__iter__()`)
	-  调用迭代器的`__next__()`方法
	-  对StopIteration抛出的错误进行处理

- **迭代器的好处**
	- 实现了访问容器的统一接口
	- 不需要在遍历之前准备好整个迭代过程中的元素
		- 可以迭代无穷个元素的数据集合
		- 未知大小的数据集合

- 因此，只要创建的对象内部实现了`__iter__()`方法和`__next__()`方法，就是一个迭代器

```python
# 创建斐波那契对象
class Fibo:
    def __init__(self):
        self.a = 0
        self.b = 1

    def __iter__(self):
        return self

    def __next__(self):
        self.a, self.b = self.b, self.a + self.b
        return self.a


for index, value in enumerate(Fibo()):
    print(value)

# =========屌丝都是这样写===========

# 浪费内存的写法
def fibo(n):
    a, b = 0, 1
    res = []

    i = 0
    while i < n:
        a, b = b, a + b
        res.append(a)
        i += 1

    return res


print(fibo(10))
```

## 深入生成器
- 使用包含yield的生成器函数创建一个生成器之后，这个生成器内部就会自动的实现`__iter__()`和`__next__()`方法，即这个生成器懈怠了迭代器协议，可以进行惰性求值
- 生成器对象`g`支持的其他方法（next, send前已论述）
	- g.close()
		- 执行`g.close()`时，生成器会停止'生产'，并抛出`StopIteration`错误
		- 当从生成器中取值结束时，解释器对其进行垃圾回收时会自动调用close()方法
	- g.throw(Exception, message)
		- 在程序逻辑中，当生成器状态满足一定条件时，可以使用`g.throw()`方法抛出一个错误
			- exception用来指定错误类型，再参数列表中是一个对象
			- message时错误信息，用字符串表示

```python
def fibo():
    a, b = 0, 1
    while 1:
        a, b = b, a + b
        yield a


gene_f = fibo()

i = 0
while 1:
    print(next(gene_f))

    if i == 10:
        # gene_f.throw(StopIteration, '10 times ')
        gene_f.close()
    i += 1
```


## 讨论一下双层循环（多层）的结束问题
- 结束双层循环有多种方式，这里列出三种方式，分别对每一种方式的“可读性”，“性能”进行分析

#### 假设
- 假设现在循环的次数非常多

#### 代码

```python
from multiprocessing import Process
import timeit

setup = 'n = 10000'


def g():
    for i in range(10000):
        for j in range(i):
            if j == i == 5000:
                return


statement1 = '''\
for i in range(n):
    for j in range(i):
        if j == i == 5000:
            break
    else:
        continue
    break
'''

statement2 = '''\
flag = False
for i in range(n):
    for j in range(i):
        if j == i == 5000:
            flag = True
            break
    if flag:
        break
'''
statement3 = '''\
try:
    for i in range(n):
        for j in range(i):
            if j == i == 5000:
                raise Exception('找到9啦')
except Exception as e:
    pass
'''

time_fuc = timeit.timeit('g()', 'from __main__ import g', number=1)
time_continue = timeit.timeit(stmt=statement1, setup=setup, number=1)
time_flag = timeit.timeit(stmt=statement2, setup=setup, number=1)
time_try = timeit.timeit(stmt=statement3, setup=setup, number=1)

print(f'time_fuc:  {time_fuc}')
print(f'time_continue: {time_continue}')
print(f'time_flag: {time_flag}')
print(f'time_try: {time_try}')

```

#### 分析
- 上面的结果看，这些方法中使用continue貌似更快些，而对于使用了额外变量flag的方式终结循环，花费时间最多
- 分析如下
	- 对于flag这种方式
		- 可读性：一般
		- 性能： 每一次循环都需要判断一次flag，处理量比较庞大的数据时，每一次判断显然都会损耗时间
	- 对于continue
		- 可读性： 较差
		- 性能： 较好
	- 对于函数
		- 可读性： 较好
		- 性能：较好
	- 对于try
		- 可读性：较好
		- 性能： 较好
		- 注意， 使用try对于多层循环时，我们可以自定义错误类型，当满足我们需要的某种类型的时候，抛出错误，获取这个值，但是使用这样方法的场景较少

#### 补充
- 可能时测试环境问题，在之后的测试中，结果变成了这样
	- 因此在生产环境中，可以按照具体测试结果进行选择

```python

```

