# contextlib模块

## with语句及其运行原理
- python中能够使用with进行上下文管理的对象有file-like object（包括文件句柄，stream流，socket套接字等）， 锁等。这是因为这些对象的类中定义了`__enter__()`和`__exit__()`的方法。
- `__enter__(self)`
	- 当对一个对象或表达式(表达式返回的对象)使用with语句进入上下文环境后，会最先运行`__enter__()`方法，这个方法可以将对象作为返回值赋值给with语句中对象的别名(as xxx)。如果没有在`__enter__()`中返回对象`return self`，那么别名拿到的是`None`。
	
- `__exit__(self, exc_type, exc_val, exc_tb)`
	- `__exit__()`用来捕获上下文运行过程中出现的异常，在该函数定义时可以针对不同一场采取不同处理。其中
		- exc_type: 异常类型
		- exc_val: 	异常值
		- exc_tb:  tracback栈信息

```python
class Query:
    def __init__(self, name):
        self.name = name

    def __enter__(self):
        print('eter begin')
        print('self', self)
        # return self           # 需要将结果返回给别名对象

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type:
            print('error')
        else:
            print('end')

    def query(self):
        print(f'wuery ingo about {self.name}')


my_query = Query('ziawang')

# with my_query as q:
#     print('q', q)
#     q.query()					# 没有return self 的情况下会报错

with my_query:
    print('my query', my_query)
    my_query.query()
```
	
## contextlib模块
#### 抽象类
- contextlib模块中提供了一个抽象基类，内部实现了`object.__enter__()`和`object.__exit__()`方法。并且前者返回对象本身，后者返回None。
	- 源码如下

```python
class AbstractContextManager(abc.ABC):

    """An abstract base class for context managers."""

    def __enter__(self):
        """Return `self` upon entering the runtime context."""
        return self

    @abc.abstractmethod
    def __exit__(self, exc_type, exc_value, traceback):
        """Raise any exception triggered within the runtime context."""
        return None
```


#### contextmanager装饰器
- contextmanager装饰的是一个生成器函数，在这个生成器函数中
	- This iterator must yield exactly one value, which will be bound to the targets in the with statement’s as clause。 yield 返回值用来给目标对象的别名绑定对象。
	- At the point where the generator yields, the block in the with statement is executed. yield返回后，会执行with内的代码。注意，如果在执行过程中抛出了异常，可以考虑采用try...except...finally分支来处理异常

```python
from contextlib import contextmanager


class MyTask:
    def __init__(self, name):
        self.name = name

    def say_name(self):
        print('-------------say begin----------------')
        print(f'this is {self.name} speaking . . .')
        print('in say function')
        print('-------------say end------------------')

    @contextmanager
    def task(self):
        print('task running ', self)
        yield self
        print('task done . . . ')


ziawang = MyTask('ziawang')
with ziawang.task() as t:
    ziawang.say_name()
```


#### closing(thing)
- closing(thing)在上下文中关闭thing对象。这要求thing对象需要有`thing.close()`方法
- 等价于

```python
from contextlib import contextmanager


@contextmanager
def my_closing(thing):
    try:
        print('context manager begin . . .')
        yield thing
    finally:
        print('context manage end')
        thing.close()

with my_closing(open('test', 'w', encoding='utf-8')) as  fw:
    print('----------begin write into file -----------')
    fw.write('这是第一行内容xxxxxxxxxxx\n'
             '这是第二行内容aaaaaaaaaaa\n'
             '这是第三行内容vvvvvvvvvvv')
    print('----------write end -----------')
```

- 直接掉用现成的closing就好

```python
from contextlib import contextmanager


with closing(open('test', 'w', encoding='utf-8')) as  fw:
    print('----------begin write into file -----------')
    fw.write('这是第一行内容xxxxxxxxxxx\n'
             '这是第二行内容aaaaaaaaaaa\n'
             '这是第三行内容vvvvvvvvvvv')
    print('----------write end -----------')
```

#### suppress(*exceptions)
- suppress意为阻止，此上下文管理器用于阻止抛出可以预见的错误，让程序往正确的方向走而不被中断
	- 官方解释：
		- this context manager should be used only to cover very specific errors where silently continuing with program execution is known to be the right thing to do.

- 可以使用`try...except...finally...`实现

```python
try:
    rf = open('xxx', 'r', encoding='utf-8')
except FileNotFoundError:
    pass
finally:
	rf.close()
```

- 使用suppress(*expections)实现

```python
from contextlib import suppress

with suppress(FileNotFoundError):
    with open('xxx', 'r', encoding='utf-8') as rf:
        for line in rf:
            print(line)
```

#### redirect_stdout(new_target)
- 用来重定向标准输出
	- new_target 是一个filelike object

```python
from contextlib import redirect_stdout
from io import StringIO


f = StringIO()
with redirect_stdout(f):
    help(redirect_stdout)
print(f)
print(f.getvalue())


rf = open('help_redirect', 'w')
with redirect_stdout(rf):
    help(redirect_stdout)
```

#### redirect_stderr(new_target)
- 用法同`redirect_stdout(new_target)`


#### ContextDecorator


#### ExitStack