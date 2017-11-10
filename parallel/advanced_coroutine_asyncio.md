## 为什么要用asyncio
#### 10K问题
- 所谓10K，就是并发处理10000个进程，在Nginx尚未流行时，想要正常并发10K个进程需要耗费的成本是非常巨大的。对于操作系统来说，其最珍贵的资源就是用来分配给程序的进程，要处理高并发仅一开进程是很难解决问题的，针对这个问题，出现了I/O多路复用
	- []()

###### I/O多路复用
- I/O多路复用，其实就是用一个进程或者线程同时处理若干个链接，通过轮询或通知的方式，当进程或线程下的任务处于就绪态时，该任务才会被处理。
	- select
		- 通过轮询的方式循环处理各个链接，每一个链接都对应着一个文件描述符。轮询过程中检查任务状态，任务处于就绪态就会被处理。
		- 缺点：
			1. 单进程下，能同时轮询处理的链接个数是有限的。
			2. 轮询的效率较低(对socket套接字是线性扫描)
			3. 需要维护一个用来存放大量描述符的数据结构，这个数据结构在用户空间和内核空间在复制传递的时候耗费较大的开销
	- poll
		- 与select类似，但是它监听的文件描述符是存储在链表中的，因此没有最大文件描述符限制
		- 缺点
			1. 水平触发，只要链表中由就绪态的链接存在，就会通知进程，如果进程没有处理该链接，他就会在下一次轮询检测到时继续通知
			2. 用户空间和内核空间数据的复制传递开销太大
	- epoll
		- 使用一个文件描述符管理多个文件描述符，用户空间与内核空间的数据复制只需要一次，因为内核的事件表中存放了用户关系描述符的事件
		- epoll支持边缘触发和水平触发，边缘出发即当一个任务处于就绪态的时候，epoll只会通知进程一次
		- 优点
			- 没有最大链接限制
			- 采用通知的方式，效率远高于轮询的方式
			- 复制开销小，epoll利用mmap()文件映射，加速用户空间与内核空间之间消息的传递


#### 根本思路
- **高效的利用阻塞，让CPU做核心任务，即高并发中内核是解决问题的所在**
	- 让内核处理控制层
	- 让应用程序处理数据层
		- 数据层即数据包处理、内存管理、处理器调度等任务

- 基于上述思路需要解决的问题
	1. 系统调度代价较高
	2. 进程/线程作为处理单元，当处理千万甚至亿级并发时，创建和清除所需要的开销太高

- 解决上面问题——协程
	- 更轻量级的处理单元
	- 安全不需要锁


#### 事件循环



## asyncio
- asyncio模型就是下实现一个消息循环，通过`asyncio.get_event_loop()`获取一个可以存放多个corontine的循环池，调用循环池对象的`loop.run_until_complete()`将任务添加到循环池中


#### event_loop 事件循环
- 程序开启的无线循环，将函数（任务）注册到event_loop上，调用相关函数异步执行任务

#### corontine 协程
- 协程对象。
- 由async关键字定义的函数在调用时会返回一个corontine协程对象
- 协程对象需要注册到事件循环中中才能被事件异步调用
- 注意：
	- 协程对象不能直接运行，当`event_loop.run_until_complete(future)`调用一个协程的时候，实际上是将协程包装成了一个task对象。


#### task 任务
- 使用async关键字或`@asyncio.corontine`创建的是原生corontine协程，task则是将协程的状态进一步封装。
- task对象是Future的一个子类，保存了协程运行之后的状态，用来获取协程任务运行的结果

###### task对象有两种状态
- 当task对象没有被注册到事件循环中时，task对象的状态是`pending`
- 当task对象被注册到事件循环后，运行结束时，task对象的状态是`finished` 

```python
import asyncio
import time

now = lambda :time.time()

async def do(x):
    print('wating:', x)

start = now()

corontine = do(1)
loop = asyncio.get_event_loop()
task = loop.create_task(corontine)
print(task)							# pending
loop.run_until_complete(task)
print(task)							# finished
```



###### 创建一个task对象
- **event_loop.create_task(corontine)**
	- Schedule the execution of a coroutine object: wrap it in a future. Return a Task object.
- **asyncio.ensure_future(corontine | future)**
	- Schedule the execution of a coroutine object: wrap it in a future. Return a Task object.
	- If the argument is a Future, it is returned directly.

#### future
- 将来或者尚未执行的任务的结果
- future对象的结果可以使用`future.result()`获取


#### async/await关键字
> async对应`@async.corontine`, await对应`yield from `

- async： 定义协程的关键字，相当于`@asyncio.corontine`
- await： 挂起当前协程并调用其它协程的功能接口
	- 当前执行的协程中一旦遇到了`await`关键字，就会挂起当前协程，切换到await关键字指定的协程，直到其他协程运行完毕或者也被挂起再进行下一个协程



## 使用async.coroutine和yield from实现异步IO

```python
import asyncio


@asyncio.coroutine
def foo():
    print('foo running ....')
    yield from asyncio.sleep(1)
    print('foo done . . . ')


loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait([foo(), foo()]))			# 将协程注册到事件循环
loop.close()
```

- `@asyncio.corontine`装饰器将foo生成器函数标记为一个corontine，在这个corontine内部，通过使用`yield from`来调用另外一个corontine协程
- python新版本中，直接提供了`async`和`await`关键字分别用来替代`@async.corontine`和`yield`，因此，上面的代码可以这样写
- asyncio实现并发，就需要多个协程来完成任务，每当有任务阻塞的时候就await，然后其他协程继续工作。创建多个协程的列表，然后将这些协程注册到事件循环中。

```python
import asyncio
import time


async def a():
    print('aaa begin')
    await asyncio.sleep(0.5)
    print('aaa end ')


async def b():
    print('bbb begin')
    await asyncio.sleep(0.5)
    print('bbb end ')


async def c():
    print('ccc begin')
    # await asyncio.sleep(0.5)
    print('ccc end')


now = lambda :time.time()
start = now()

event_lopp = asyncio.get_event_loop()
taskList = [a(), b(), c()]
event_lopp.run_until_complete(asyncio.wait(taskList))
event_lopp.close()
```

## 给task绑定回调
#### Task.add_done_callback(future)
- Add a callback to be run when the future becomes done.     
	- **The callback is called with a single argument - the future object.** 
	- If the future is already done when this is called, the callback is scheduled with call_soon.
- 使用`task.add_done_callback(func)`来给task对象添加一个函数作为回调函数，func函数的参数只能有一个，且为future对象

```python
import time
import asyncio

now = lambda : time.time()

async def do():
    print('Waiting: ')
    return 'haha'

def callback(future):
    print('Callback: ', future.result())

start = now()

coroutine = do()
loop = asyncio.get_event_loop()
task = asyncio.ensure_future(coroutine)
task.add_done_callback(callback)
loop.run_until_complete(task)

print('TIME: ', now() - start)
```

#### 直接使用task.result()获取结果
- 当task的状态称为`finish`的时候，直接通过`task.result()`就可以获取协程运行的结果

#### 使用functools模块为回调函数增加参数

- 待补充




## 异步协程实现并发
#### 并发与并行
- 并行是只能存在于多核计算机中，在同一时刻多个CPU可以处理多个任务
- 并发是一个CPU不断在多个任务之间快速切换，从而达到多个任务同时进行的目的，并发的实现依靠多道技术



#### asyncio.wait(tasks) 与 async.gather(*task)
- 两者都可以将多个任务封装成task注册到事件循环中，不同的是，前者可以接收一个task列表，后者可以接收多个task

```python
import asyncio
import time


async def a():
    print('aaa begin')
    await asyncio.sleep(0.5)
    print('aaa end ')


async def b():
    print('bbb begin')
    await asyncio.sleep(0.5)
    print('bbb end ')


async def c():
    print('ccc begin')
    await asyncio.sleep(0.5)
    print('ccc end')


now = lambda :time.time()
start = now()

event_lopp = asyncio.get_event_loop()
taskList = [a(), b(), c()]
event_lopp.run_until_complete(asyncio.wait(taskList))
# event_lopp.run_until_complete(asyncio.gather(*taskList))
event_lopp.close()

print(now() - start)

```

- 注意，两者返回值是不一样的
	- `asyncio.wait()`返回值是一个元组，(finished_task_set, pending_task_set)，即处于finish状态的task和处于pending状态的task的集合。task内封装了协程的运行状态，想要获取协程的结果，需要调用遍历并调用task的`result()`方法
	- `async.gather()`返回值是一个列表，存放了协程函数的返回值

```python
import asyncio
import time

now = lambda: time.time()


async def do():
    print(" let's do it ! ")
    await asyncio.sleep(0.5)
    print('done....')
    return 'hello'


async def main():
    await asyncio.sleep(0.5)

    taskList = [
        asyncio.ensure_future(do()),
        asyncio.ensure_future(do()),
        asyncio.ensure_future(do())
    ]

    # dones, pendings = await asyncio.wait(taskList)
    # result = await asyncio.wait(taskList)
    result = await asyncio.gather(*taskList)
    print(result)
    for res in result:
        print('result : ', res)
```



#### 嵌套协程
- 使用async定义的协程函数内部仍然可以定义其他的协程


```python
import asyncio
import time

now = lambda: time.time()


async def do():
    print(" let's do it ! ")
    await asyncio.sleep(0.5)					# 模拟协程内阻塞IO
    print('done....')
    return 'hello'


async def main():
    await asyncio.sleep(0.5)					# 模拟协程内阻塞IO

    taskList = [
        asyncio.ensure_future(do()),
        asyncio.ensure_future(do()),
        asyncio.ensure_future(do())
    ]

    dones, pendings = await asyncio.wait(taskList)


    for task in dones:
        print('result : ', task.result())

start = now()

loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait([main(), main()]))
loop.close()

print('total cost time: ', now()-start)		# 1.0035514831542969
```

- 上述结果是在main()内部就获取了do协程的结果，如果不在main()内部获取，而是将`async.wait()`或者`async.gather()`返回的结果作为返回值，返回给main()的调用，那么得到的结果将是main()协程对应的内容
	- 注意，`async.wait()`和`async.gather()`返回的结果不同

```python

```

###### asyncio.as_completed()
- 获取一个协程的列表(参数),同时返回一个按完成顺序生成协程的迭代器，协程一旦运行结束，马上处理结果,因此当用它迭代时，会尽快得到每个可用的结果

```python
import asyncio
import time

now = lambda: time.time()


async def do():
    print(" let's do it ! ")
    await asyncio.sleep(0.5)
    print('done....')
    return 'hello'


async def main():
    await asyncio.sleep(0.5)

    taskList = [
        asyncio.ensure_future(do()),
        asyncio.ensure_future(do()),
        asyncio.ensure_future(do())
    ]

    result = asyncio.as_completed(taskList)
    print(result)

    for task in result:
        res = await task
        print(res)

start = now()

loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait([main()]))
loop.close()

print('total cost time: ', now()-start)
```

## Executor
- Call a function in an Executor (pool of threads or pool of processes). By default, an event loop uses a thread pool executor (ThreadPoolExecutor).

#### eventloop.run_in_executor(executor, func, *args)
- 在事件循环中，使用executor异步执行func任务
- parameters
	- executor一个executor对象，默认 ThreadPoolExecutor实例
	- args为func要传入的参数

#### eventloop.set_default_executor(executor)
- 设置默认的executor


