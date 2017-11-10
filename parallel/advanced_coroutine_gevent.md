# gevent
- gevent是一个基于微线程库Greenlet的并发框架，使用gevent提供的Monkey Patch方法可以在运行时动态修改标准库中大部分阻塞式系统调用。并且gevent为我们实现了一个基于gevent的socket、select等模块，充分发挥协程的威力

- 不论是使用yield还是使用greenlet，都无法实现程序遇到阻塞时切换到其他非阻塞任务上的效果，但是gevent帮我们实现了。


## gevent.spawn(func, *args, **kwargs)
- 返回一个greenlet对象并指向该对象的任务
- parameters
	- func：协程任务
	- *args, **kwargs为要传入func的参数


> gevent的spawn方法其实就是Greenlet的spawn方法，该方法直接创建一个Greenlet的实例，



## gevent.joinall(greenlets, timeout=None, raise_error=False, count=None)
- 阻塞当前线程至所有协程（greenlet）完成任务
- parameters	
	- greenlets： 存放greenlet对象的序列，可以是列表、元组
	- timeout： 阻塞的最大时长
	


## gevent.sleep(num)
- 与time.sleep(num)不同，当我们以time,sleep()模拟IO阻塞的时候，gevent无法识别出time.sleep()是IO的，有两种方法可以解决
	1. 使用gevent自带的`gevent.sleep()`代替`time.sleep()`
	2. 在程序开始时，导入Monkey Patch
		- `from gevent impoet monkey; monkey.patch_all()`

- 注意： 即使在程序中出现`gevent.sleep(0)`，gevent会将其视为阻塞，挂起该任务并切换

```python

```

## gevent异步处理协程任务

```python

import gevent
import random
import time

def task(pid):
    gevent.sleep(random.randint(0, 3)*0.01)
    print('task %s done' % pid)

def synchronous():
    for i in range(1, 10):
        task(i)

def asynchronous():
    threads = [gevent.spawn(task, i) for i in range(1, 10)]
    gevent.joinall(threads)

start1 = time.time()
print('synchronous begin')
synchronous()
print('synchronous cose: %s' %(time.time()-start1))


start2 = time.time()
print('asynchronous begin')
asynchronous()
print('asynchronous cost: %s' % (time.time() - start2))

```

## 创建Greenlet的子类(了解即可)
- gevent.spawn()会返回一个greenlet对象，我们通过创建Greenlet的子类也可以获取一个greenlet对象

```python
import gevent
from gevent import Greenlet


class MyGreenlet(Greenlet):
    def __init__(self, message, n):
        super().__init__()
        self.message = message
        self.n = n
    
    def _run(self):
        print(self.message)
        gevent.sleep(self.n)

gr = MyGreenlet('hello', 1)
gr.start()
gr.join()
```



## Event事件
- `from gevent.event import Event`
- gevent中的事件提供了和`multiprocessing.Event`相同的功能调用接口，当事件处于`wait()`状态并且没有被`set()`激活的时候，当前协程任务会被阻塞

```python
import gevent
from gevent.event import Event

evt = Event()

def setter():
	print('wait me for  3 seconds')
	gevent.sleep(3)
	print('ok , I am ready')
	evt.set()

def waiter():
	print('wait for you!!!')
	evt.wait()		# 阻塞
	print("I's time to see you !")

def main():
	gevent.joinall([
		gevent.spawn(setter),
		gevent.spawn(waiter),
		gevent.spawn(waiter),
		gevent.spawn(waiter),
		gevent.spawn(waiter),
		gevent.spawn(waiter)
	])



if __name__ == '__main__':
	main()

```


## gevent 实现的其他数据结构
- 除了Event，gevent还实现了与multiprocessing和threading中相同的功能接口
	- Pool
	- Queue
	- Lock
	- Semaphore
	- Local（类似thread.Local）
	- Subprocess
	- Actor

> 这些数据结使用起来和在multiprocessing、threading中使用的方式完全相同