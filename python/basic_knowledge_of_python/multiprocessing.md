
# multiprocessing 模块

[点击查看我整理的multiprocessing模块思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/multiprocessing-2.png?raw=true)

> 与线程不同，进程本身没有任何共享状态，进程修改的数据，改动仅限于该进程内。


## The Process Class
> spawned  大量生产的  
> processes are spawned by creating a Process object and then calling its start() method.  

- Note:
	- the `if __name__ == '__main__' `part is necessary


> semantic       语义，语法   
> daemon         守护天使   


## Process object
- multiprocessing.Process(group=None, target=None, name=None, args=(), kwargs={}, \*, daemon=None)
	- **constructor should always be called with keyword arguments**
	- groups:
		- should always be None
	- target:
		- target is the callable object to be invoked by the run() method
	- name:
		- process’s name.The name is a string used for identification purposes only
	- args
		- the argument tuple for the target invocation
	- keargs
		-  a dictionary of keyword arguments for the target invocatio
	- daemon	
		- sets the process daemon flag to True or False.
		- if None (the default), this flag will be inherited from the creating process.
	- **NOTE:**
		- If a subclass overrides the constructor, it must make sure it invokes the base class constructor (Process.__init__()) before doing anything else to the process.
		- args中只有一个元素的时候，该元素后面不要忘了加上一个逗号，丑陋但是有效
		- windows系统中，进程的创建和开启要在`if __name__ == '__main__'`代码块内执行，不然会报错
		

1. 看例子，注意观察子进程与主进程的顺序

```python

import time
import os
from multiprocessing import Process


def f(process_name):
    print('%s  start......'%process_name, os.getpid(), os.getppid())
    time.sleep(2)    
    print('........%s   end'%process_name, os.getpid(), os.getppid())


f1 = Process(target=f, args=('f1', ))
f2 = Process(target=f, kwargs={'process_name':'f2'})
f3 = Process(target=f, args=('f3',))


if __name__ == "__main__":
    print('主进程开始', os.getpid())
    f1.start()
    f2.start()
    f3.start()
    print('主进程中的内容')
-----------------------------------------------------------------------------------------------
# 运行结果
主进程开始 9968
主进程中的内容
f2  start...... 6372 9968
f3  start...... 10560 9968
f1  start...... 6952 9968
........f2   end 6372 9968
........f3   end 10560 9968
........f1   end 6952 9968

Process finished with exit code 0

```

- 当子进程调用start()方法时候，会将子进程的内存加载到内存（，如果是Linux平台上，还会将父类的内存空间中的内容copy到自己内存空间中），这样子进程就会产生一个IO阻塞，操作系统就会调用其他进程去使用CPU

2. 当将出主进程时间延长的时候，再看下面例子

```python
from multiprocessing import Process
import time
import os


def f(process_name):
    print('%s  start......'%process_name, os.getpid(), os.getppid())
    time.sleep(2)   
    print('........%s   end'%process_name, os.getpid(), os.getppid())

f1 = Process(target=f, args=('f1', ))
f2 = Process(target=f, kwargs={'process_name':'f2'})
f3 = Process(target=f, args=('f3',))


if __name__ == "__main__":
    print('主进程开始', os.getpid())
    f1.start()
    f2.start()
    f3.start()
    time.sleep(3)      # time.sleep()也是主程序中的一部分，这里作用就是为了延长主进程运行时间
    print('主进程中的内容')
----------------------------------------------------------------------------------------------------
# 运行结果
主进程开始 1760
f1  start...... 10924 1760
f2  start...... 4960 1760
f3  start...... 6456 1760
........f1   end 10924 1760
........f2   end 4960 1760
........f3   end 6456 1760
主进程中的内容

Process finished with exit code 0
```

3. 创建一个Process类的子类

```python
class MyProcess(Process):
    def __init__(self,name):
        self.name = name
        super().__init__()

    def run(self):
        print('%s  start......' % self.name, os.getpid(), os.getppid())
        time.sleep(2)
        print('........%s   end' % self.name, os.getpid(), os.getppid())

if __name__ == '__main__':
    print('pycharm进程id', os.getppid())
    print('当前脚本进程id', os.getpid())
    f1 = MyProcess('f1')
    f2 = MyProcess('f2')
    f3 = MyProcess('f3')
    f1.start()
    f2.start()
    f3.start()
---------------------------------------------------------------------------------------
# 运行结果
pycharm进程id 9720
当前脚本进程id 8232
MyProcess-2  start...... 10588 8232
MyProcess-1  start...... 9556 8232
MyProcess-3  start...... 7736 8232
........MyProcess-2   end 10588 8232
........MyProcess-1   end 9556 8232
........MyProcess-3   end 7736 8232

Process finished with exit code 0
```

> activity   活动  
> respectively    各自的，分别的    
> sequential      按顺序的，按次序的   

## process_obj.Methods
- run()
	- representing the process’s activity.
	- can override this method in a subclass.
		- invokes the callable object passed to the object’s constructor as the target argument
		- if any, with sequential and keyword arguments taken from the args and kwargs arguments, respectively.


> arrange    准备  


- start()
	- start the process's activity
	- must be called at most once per process object
	-  It arranges for the object’s run() method to be invoked in a separate process.
		-  start作用只是发起开启进程的请求，何时进程开始运行是由操作系统调度的（当该进程的内存空间准备完毕的时候，就从阻塞态编程了就绪态，处于就绪态进程的任务可以被CPU处理，但是不一定会立即处理），特别的，当有多个进程一起start发起请求的时候，进程开启的顺序并不是按照start的先后顺序来安排的
		-  一个进程start之后，先该进程创建以一个内存空间，将对应的数据加载到内存中之后，然后该进程的任务才能开始执行，这需要时间。
			- 看下面例子，主进程不会等子进程加载完成后执行完再运行主进程自己的代码，而是会在子进程start方法执行完后，继续运行主进程中的其他代码，当主进程运行过程中子进程启动成功并可以执行

```python
# 主进程代码运行完，子进程才刚开始
from multiprocessing import  Process
import time
import os


def task():
    print('%s runing'%os.getpid())
    time.sleep(2)
    print('%s  done . . .   '%os.getpid())


if __name__ == '__main__':
    p = Process(target=task)
    p.start()
    print('主进程')
-----------------------------------------------------------------
主进程
7688 runing
7688  done . . .   

Process finished with exit code 0
```


```python
# 主进程代码执行过程中，子进程启动成功
from multiprocessing import  Process
import time
import os


def task():
    print('%s runing'%os.getpid())
    time.sleep(2)
    print('%s  done . . .   '%os.getpid())


if __name__ == '__main__':
    p = Process(target=task)
    p.start()
    time.sleep(2)
    print('主进程')
----------------------------------------------------------
9728 runing
主进程
9728  done . . .   

Process finished with exit code 0
```





> optonal   可选择的   
> block      阻塞   


- join([timeout])
	- Wait until the process terminates
	- blocks the calling process until the process whose join() method is called terminates – either normally or through an unhandled exception – or until the optional timeout occurs.
		- 这个方法会阻塞调用进程，直到当前进程正常执行完，或者出现异常，或者直到超出timeout限制的时候，其他进程才能开始运行

```python
import time
import os
from multiprocessing import Process


def f(process_name):
    print('%s  start......'%process_name, os.getpid(), os.getppid())
    time.sleep(2)     #
    print('........%s   end'%process_name, os.getpid(), os.getppid())


f1 = Process(target=f, args=('f1', ))
f2 = Process(target=f, kwargs={'process_name':'f2'})
f3 = Process(target=f, args=('f3',))


if __name__ == "__main__":
    print('主进程开始', os.getpid())
    f1.start()
    f1.join()
    f2.start()
    f3.start()
    time.sleep(4)     
    print('主进程中的内容')

--------------------------------------------------------
# 运行结果
主进程开始 3060
f1  start...... 11128 3060     
........f1   end 11128 3060
f3  start...... 10932 3060
f2  start...... 4528 3060       
........f3   end 10932 3060
........f2   end 4528 3060
主进程中的内容

Process finished with exit code 0

```


> descendant      后代的，子代的   
> orphaned        孤立的    
> corrupt         破坏
> liable        有责任的  

- 注意，join()方法只能阻塞由start方法开启的进程，不能够阻塞住由run方法开启的进程
	- 因为只由run()方法开启的进程，没有分配到进程id，join()想要阻塞该进程先要通过进程id找到该进程，所以如果用run方法开启的该进程，就没法join

```python
from multiprocessing import  Process
import time


class Myprocess(Process):
    def run(self):
        print('%s run'%self.pid)
        time.sleep(1)
        print('%s end'%self.pid)



if __name__ == '__main__':
    p = Myprocess()
    # p.start()
    p.run()
    p.join()
    print('这是主进程')
-----------------------------------------------------------------------------------
None run
None end
Traceback (most recent call last):
  File "D:/files/python/practice.py", line 144, in <module>
    p.join()
  File "D:\Python36\lib\multiprocessing\process.py", line 120, in join
    assert self._popen is not None, 'can only join a started process'
AssertionError: can only join a started process

Process finished with exit code 1
```

- 还有一个现象，如果start开启某个进程之后，再用run再次执行一次进程任务，此时的run会得到进程号，并且该任务会被执行两次(两次run)

```python
from multiprocessing import  Process
import time


class Myprocess(Process):
    def run(self):
        print('%s run'%self.pid)
        time.sleep(1)
        print('%s end'%self.pid)



if __name__ == '__main__':
    p = Myprocess()
    p.start()
    p.run()
    p.join()
    print('这是主进程')
---------------------------------------------------------------------
7740 run
7740 run
7740 end
7740 end
这是主进程

Process finished with exit code 0
```


- terminate()
	- Terminate the process.
	- On Unix this is done using the SIGTERM signal; on Windows TerminateProcess() is used
	- **If this method is used when the associated process is using a pipe or queue then the pipe or queue  become corrupted and may become unusable by other process. Similarly, if the process has acquired a lock or semaphore etc. then terminating it is liable to cause other processes to deadlock.**
		- 如果被终结的进程有子进程，那么该子进程就会成为僵尸进程
		- 如果该进程任务使用着Queue队列或Pipe管道，终结该进程会导致队列或管道被破坏，其他进程就无法继续使用该队列或管道
		- 如果该进程中使用了锁，终止该进程可能会导致该锁无法释放，出现死锁


- is_alive()
	- Return whether the process is alive.
	- a process object is alive from the moment the start() method returns until the child process terminates.
		- 操作系统关闭该进程需要时间，如果调用此方法后立即调用is_alive()，可能会返回True


```python
import time
from multiprocessing import  Process



class Myprocess(Process):
    def run(self):
        print('%s run'%self.pid)
        time.sleep(10)
        print('%s end'%self.pid)


if __name__ == '__main__':
    p = Myprocess()
    p.start()
    p.terminate()
    print(p.is_alive())
    time.sleep(1)
    print(p.is_alive())
```



## process_obj.variables
- pid
	- Return the process ID. Before the process is spawned, this will be None.
	- 使用起来比os.getpid()方便
- name
	- 进程的名称
- daemon
	- 默认False
	- 设置为True则表示p为守护进程
	- 守护进程必须在p.start()之前设置
- p.exitcode
	- 当进程运行时，该变量值为None
	- 当该变量值为-N时，表示被信号N结束
- p.authkey
	- 进程的身份验证键
	- 值为os.urandom()随机生成的32字符的字符串
	- 用于为网络连接底层进程间通信提供安全，这类链接只有在相同身份验证键时才能成功




> primitive           原始的   
> as long as      
> ubbounded      不限制的  
> release          释放   

## Lock object
- 创建方式`l = Lock()`

- lock.acquire()
	- Acquire a lock
	- block until release.
- lock.relase()
	-  Release a lock
		-  When the lock is locked, reset it to unlocked

```python
# test.txt文件内容
{"count":6}
-----------------------------------------------------
import os
import json
from multiprocessing import Lock, Process

# 模拟抢票系统


def search():
    stock_info = json.load(open('test.txt'))
    print('剩余%s张票'%stock_info['count'])


def get():
    stock_info = json.load(open('test.txt'))
    if stock_info['count'] > 0:
        stock_info['count'] -= 1
        json.dump(stock_info, open('test.txt', 'w'))
        print('%s购买成功'%os.getpid())
    else:
        print('%s购买失败'%os.getpid())

def run(l):
    search()
    l.acquire()
    get()
    l.release()

if __name__ == '__main__':
    l = Lock()
    for i in range(10):
        p = Process(target=run, args=(l, ))
        p.start()

---------------------------------------------------------------------------------------
剩余6张票
10160购买成功
剩余5张票
4136购买成功
剩余4张票
4592购买成功
剩余3张票
剩余2张票
5192购买成功
剩余2张票
剩余2张票
6396购买成功
6352购买成功
剩余0张票
8712购买失败
1636购买失败
剩余0张票
4760购买失败
剩余0张票
7248购买失败

Process finished with exit code 0
```


> shared     共享的   
> buffer       缓冲区   
> feeder      分支  
> approximate     大概的   
> concurrent    同时存在的   

# sharing state between multiple processes
> when doing concurrent programming it is usually best to avoid using shared state as far as possible. This is particularly true when using multiple processes.
> if you really do need to use some shared data then multiprocessing provides a couple of ways of doing so.

- shared memory(Value, Array)  
- server process (Manager object)
- queue  (Queue, JoinableQueue)


## Queue object
- Queue([maxsize])
	- Returns a process shared queue implemented using a pipe and a few locks/semaphores
	- When a process first puts an item on the queue a feeder thread is started which transfers objects from a buffer into the pipe.
		- 当maxxize不指定的时候，返回的Queue对象长度不限制 


```python
from multiprocessing import Process, Queue
import time
import os
import random


def my_put(q):
    for i in range(10):
        q.put(i)
        time.sleep(random.randint(1, 3))
        print('%s put  %s into the Queue '%(os.getpid(), i))

def my_get(q):
    while True:
        res = q.get()
        time.sleep(2)
        print('%s get  %s from the Queue'%(os.getpid(), res))
if __name__ == '__main__':
    q = Queue(5)
    p = Process(target=my_put, args=(q, ))
    g = Process(target=my_get, args=(q, ))
    p.start()
    g.start()
    print('这是主进程')
------------------------------------------------------------------------------------
这是主进程
3172 put  0 into the Queue 
3252 get  0 from the Queue
3252 get  1 from the Queue
3172 put  1 into the Queue 
3252 get  2 from the Queue
3172 put  2 into the Queue 
3172 put  3 into the Queue 
3252 get  3 from the Queue
3172 put  4 into the Queue 
3252 get  4 from the Queue
3172 put  5 into the Queue 
3172 put  6 into the Queue 
3252 get  5 from the Queue
3252 get  6 from the Queue
3172 put  7 into the Queue 
3252 get  7 from the Queue
3172 put  8 into the Queue 
3252 get  8 from the Queue
3172 put  9 into the Queue 
3252 get  9 from the Queue
```


## Queue  method  
- 不稳定结果的方法
	- q.qsize()
		- Return the approximate size of the queue
	- q.empty()
		- Return True if the queue is empty
	- q.full()
		- Return True if the queue is full


> slot      槽位，位置   


- q.put(obj, block=True, timeout=None)
	- obj is arbitrary value of python
	- block=True  & time = None
		-   block if necessary until a free slot is available
	- block=True & time is a positive number
		-    blocks at most timeout seconds and raises the queue.Full exception if no free slot was available within that time.
	-  block=False 
		-  Note: timeout argument is ingure in this case
		-  put an item on the queue if a free slot is immediately available, else raise the queue.Full exception (timeout is ignored in that case).



```python
from multiprocessing import Queue


q = Queue(5)

for i in range(10):
    q.put(i, block=False)
    print(i)
```

```python
from multiprocessing import Queue


q = Queue(5)

for i in range(10):
    q.put(i, block=False)
    print(i)
```


- q.get(block=True, timeout=None)
	- Remove and return an item from the queue
	- block和time参数与put逻辑相同


```python
# 把Queue的长度改成0，再运行

from multiprocessing import Process, Queue
import time
import os


def my_put(q):
    for i in range(10):
        q.put(i)
        time.sleep(1)
        print('%s put  %s into the Queue '%(os.getpid(), i))

def my_get(q):
    while True:
        res = q.get()
        time.sleep(2)
        print('%s get  %s from the Queue'%(os.getpid(), res))
if __name__ == '__main__':
    q = Queue(1)
    p = Process(target=my_put, args=(q, ))
    g = Process(target=my_get, args=(q, ))
    p.start()
    g.start()
    print('这是主进程')
# 再运行会发现陈Queue中主允许同时存在一个元素，只有后档这个元素被get后，才能被put进去


```

- q.put_nowait(obj)
	- q.put(obj, block=False)

- q.get_nowait(obj)
	- q.get(obj, block=False)

- q.close()
	- Indicate that no more data will be put on this queue by the current process
		- 如果在close方法后继续执行put就会报错

```python
from multiprocessing import Queue

q = Queue(5)
q.put(1)
q.put(1)
q.put(1)
q.close()
q.put(2)
q.put(2)
-------------------------------------------------------------------------
Traceback (most recent call last):
  File "D:/files/python_practice/0821-0827/0825/practice.py", line 53, in <module>
    q.put(2)
  File "D:\Python36\lib\multiprocessing\queues.py", line 81, in put
    assert not self._closed
AssertionError


```

- join_thread()

- cancel_join_thread()



## JoinableQueue   object

> additionally    额外的   
> fromerly      之前的   
> enqueued       排队的   
> subsequent     随后的，后面的  
> fetch         获取    

- a Queue subclass, is a queue which additionally has task_done() and join() methods.




- q.join()
	- Block until all items in the queue have been gotten and processed.
	- The count of unfinished tasks goes up whenever an item is added to the queue.
	-  The count goes down whenever a consumer calls task_done() to indicate that the item was retrieved and all work on it is complete.
	-   **When the count of unfinished tasks drops to zero, join() unblocks.**

> go up  上升  

- q.task_done()
	- Note:
		- __Used by queue consumers__
	- Indicate that a formerly enqueued task is complete

```python
# 生产者消费者模型1——单个生产者和单个消费者
import time
import os
import random
from multiprocessing import Process,JoinableQueue

q = JoinableQueue(5)


def producer(q):
    for i in range(10):
        time.sleep(2)
        q.put('*')
        print('produce a  *')
    q.join()


def customer(q):
    while True:
        buy = q.get()
        time.sleep(random.randint(1, 3))
        print('customer %s '%os.getpid(), buy)
        q.task_done()


if __name__ == '__main__':
    p = Process(target=producer, args=(q, ))
    c = Process(target=customer, args=(q, ))
    c.daemon = True

    p.start()
    c.start()
    p.join()

    print('这是主进程')

# 自行运行结果
```

- **When the count of unfinished tasks drops to zero, join() unblocks**


```python
# 多个生产者消费者并发
import os
import time
import random
from multiprocessing import JoinableQueue, Process


def producer1(q):
    for i in range(3):
        time.sleep(random.randint(1, 2))
        q.put('$$%s'%i)
        print('%s produce $$%s' % (os.getpid(), i))
    q.join()

def producer2(q):
    for i in range(3):
        time.sleep(random.randint(1, 2))
        q.put('@@%s'%i)
        print('%s produce @@%s'%(os.getpid(), i))
    q.join()

def producer3(q):
    for i in range(3):
        time.sleep(random.randint(1, 2))
        q.put('**%s'%i)
        print('%s produce **%s' % (os.getpid(), i))
    q.join()

def customer(q):
    while True:
        time.sleep(2)
        buy = q.get()
        print('[%s] buy %s'%(os.getpid(), buy))
        q.task_done()



if __name__ == '__main__':
    q = JoinableQueue(5)
    p1 = Process(target=producer1, args=(q, ))
    p2 = Process(target=producer2, args=(q, ))
    p3 = Process(target=producer3, args=(q, ))

    c1 = Process(target=customer, args=(q, ))
    c2 = Process(target=customer, args=(q, ))
    c1.daemon = True
    c2.daemon = True

    for i in [p1, p2, p3, c1, c2]:
        i.start()

    for i in [p1, p2, p3]:
        i.join()

    print('这是主进程')

```


> correspond       通信  
> proxy           代替物   

## Manager   object
> 创造共享数据（字典，列表等），没有锁，各个进程对于数据是竞争使用关系
> 两个进程同时对一个共享数据进行处理，得到的结果相当于处理一次，因为进程处理的时候获取的原数据是没变的
> 可以为进程上锁来让进程之间串行，每一个进程运行时使用的都是基于上一次进程处理后的数据



- multiprocessing.Manager()
	- Note:
		- __return a multiprocessing.manages.SyncManager object__ which can be uesed for shaing objects between process
		- the returned object corresponds to a spawned child process and has methods which will create shared objects and return corresponding proxies

> A manager returned by Manager() will support types list, dict, Namespace, Lock, RLock, Semaphore, BoundedSemaphore, Condition, Event, Barrier, Queue, Value and Array. 

- m.list()     ,     m.list(sequence)
	- Create a shared list object and return a proxy for it.

```python

```

> proxy     代理   

- m.dict()   ,     m.dict(mapping)     ,     m.dict(sequence)     ,    m.dict(key = value)
	- Create a shared dict object and return a proxy for it.

```python
from multiprocessing import Process,Manager,Lock

def task(d, mutex):
    with mutex:
        d['count'] -= 1


if __name__ == '__main__':
    mutex = Lock()
    with Manager() as m:
        d = m.dict(count=500)
        p_l= []
        for i in range(500):
            p = Process(target=task, args=(d, mutex))
            p_l.append(p)
            p.start()
        for p in p_l:
            p.join()
        print(d)

```

上面一种没有给它上锁，可能会出现最后count对应的不是0的情况

```python
from multiprocessing import Process, Lock, Manager


def task(mutex, dic):
    with mutex:
        dic['count'] -= 1

if __name__ == '__main__':
    mutex = Lock()
    m = Manager()
    share_dic = m.dict(count=100)
    l = []
    for i in range(100):
        p = Process(target=task, args=(mutex, share_dic))
        p.start()
        l.append(p)
    for j in l:
        j.join()
    print(share_dic)

```


- m.Value(typecode, value)
	- Create an object with a writable value attribute and return a proxy for it.

```python


```


- m.Queue([maxsize])
	- Create a shared queue.Queue object and return a proxy for it.

- m.Lock()
	- Create a shared threading.Lock object and return a proxy for it.




## Pool  进程池
> 进程池的作用就是控制同时并发的进程数目   
> submitted         屈服， 服从   
> parallel         同步的   
> enable          使能做， 使可能  
> unused        没使用的， 空闲的  
> free             释放   

- multiprocessing.Pool([processes[, initializer[, initargs[, maxtasksperchild[, context]]]]]) 
- multiprocessing.pool.Pool([processes[, initializer[, initargs[, maxtasksperchild[, context]]]]])
	- return a multiprocessing.pool.Pool object , which  controls a pool of worker processes to which jobs can be submitted.
	- parameters
		- __processes__:
			- the number of worker processes to use
			- If processes is None then the number returned by os.cpu_count()
				- 即是计算机上的cpu数量
		- __maxtasksperchild__ :
			- the number of tasks a worker process can complete before it will exit and be replaced with a fresh worker process
			- The default maxtasksperchild is None, which means worker processes will live as long as the pool
	- Note:the methods of the pool object should only be called by the process which created the pool.
		- Pool 对象的方法只能在创建pool的进程中调用


```python
# 简单的一个进程池
import time
import  os
from multiprocessing import Pool


def task():
    print('%s worker process start do a task '%os.getpid())
    time.sleep(2)
    print('%s worker process has done a task'%os.getpid())



if __name__ == '__main__':
    p = Pool(2)
    for i in range(10):
        p.apply_async(func=task)
    p.close()
    p.join()
    print('主进程')
```


> duration     持续时间   

- ** worker process **
	- Worker processes within a Pool  live for the complete duration of the Pool’s work queue
		- 进程池中进行工作的进程，数量由processes指定，当其中一个id对应的进程完成一个队列中的任务之后，会以相同的id(即不会被回收释放)处理下一个任务


- apply(func,[ args,[ kwargs]])
	- **It blocks until the result is ready**
		- func is only executed in one of the workers (worker processes) of the pool
	- parameters
		- func is the name of the function
		- args is a tuple of the arguments
			- 注意：args传参的时候虽然不用keyword方式传参，但是传入的参数必须放在元组中
		- kwds is the dict of the arguments  
	- Call func with arguments args and keyword arguments kwds
	- apply()      =      apply_async().get()
		-  apply_async() is better suited for work in parallel


```python
# apply()串行
import time
import  os
from multiprocessing import Pool


def task():
    print('%s worker process start do a task '%os.getpid())
    time.sleep(2)
    print('%s worker process has done a task'%os.getpid())



if __name__ == '__main__':
    p = Pool(2)
    for i in range(10):
        p.apply(func=task)
    print('主进程')
-------------------------------------------------------------------------------------
2472 worker process start do a task 
2472 worker process has done a task
4640 worker process start do a task 
4640 worker process has done a task
2472 worker process start do a task 
2472 worker process has done a task
4640 worker process start do a task 
4640 worker process has done a task
2472 worker process start do a task 
2472 worker process has done a task
4640 worker process start do a task 
4640 worker process has done a task
2472 worker process start do a task 
2472 worker process has done a task
4640 worker process start do a task 
4640 worker process has done a task
2472 worker process start do a task 
2472 worker process has done a task
4640 worker process start do a task 
4640 worker process has done a task
主进程
```




> apply()同步提交任务，主进程提交任务，等进程池中工作进程完成该任务之后，主进程才能提交下次任务
> apply()将任务提交到进程池中的某一个工作进程，多个apply存在于主进程中的时候，使进程池中的进程串行运行
> 进程池中工作进程的进程id是不变的
> apply() 和 apply_async()提交的任务而不是进程
> apply_async()异步提交任务， 主进程提交任务到进程池，不需要等待任务结果，返回值是一个applyresult  object，可以继续往进程池扔进程
>  apply_async更灵活，可以多种形式的输出值，挡在进程过程中调用其返回值的get方法时候，就相当于apply，使用apply_async的时候，最好在最后再将值获取。


- apply_async(func[, args[, kwds[, callback[, error_callback]]]])
	- func , args, kwds is memtioned above
	- callback（回调函数，参考进程与线程）
		-  should be callable
		-  When the result becomes ready callback is applied to it, that is unless the call failed, in which case the error_callback is applied instead.
			-  当进程结果准备好的时候传递给callback指向的函数，如果传递失败，就发送给error_callback指向的函数
	- error_callback
		-   should be a callable
		-   If the target function fails, then the error_callback is called with the exception instance
	 - callback应该被立刻调用，除非控制运算结果的线程被阻塞了
		 - Callbacks should complete immediately since otherwise the thread which handles the results will get blocked.

- 回调函数
	- callback指向的一个函数就是回调函数，在一段程序运行完得到一个结果之后，将这个结果当作中间数据传递给另外一个函数进行处理，从而得到最终结果。
	- **回调函数存在于主进程或调用回调函数的父进程中**
	- **回调函数的应用场景——爬虫**
	- 什么时候使用回调函数
		-  使用进程池处理任务的时候，对于比较耗费时间的任务，放在进程池中，进行并发多进程处理任务，对于耗费时间较少并且需要对上述耗时任务结果进行处理的任务就需要使用回调函数


- 获取结果的两种方式

```python
# 1. 获取apply_result对象之后直接获取其结果, 此时进程之间是串行的
from multiprocessing import Pool
import time
import os

def task(n):
    print('%s worker process start do a task '%os.getpid())
    time.sleep(1)
    print('%s worker process has done a task'%os.getpid())
    return n**2

if __name__ == '__main__':
    p = Pool(2)
    for i in range(1, 10):
        obj = p.apply_async(task, args=(i, ))
        print(obj.get())
    p.close()
    p.join()
----------------------------------------------------------------------------------
1348 worker process start do a task 
1348 worker process has done a task
1
4876 worker process start do a task 
4876 worker process has done a task
4
1348 worker process start do a task 
1348 worker process has done a task
9
4876 worker process start do a task 
4876 worker process has done a task
16
1348 worker process start do a task 
1348 worker process has done a task
25
4876 worker process start do a task 
4876 worker process has done a task
36
1348 worker process start do a task 
1348 worker process has done a task
49
4876 worker process start do a task 
4876 worker process has done a task
64
1348 worker process start do a task 
1348 worker process has done a task
81

Process finished with exit code 0

```



```python
# 2. 先获取apply_result对象，再最后一次性获取所有结果
from multiprocessing import Pool
import time
import os

def task(n):
    print('%s worker process start do a task '%os.getpid())
    time.sleep(1)
    print('%s worker process has done a task'%os.getpid())
    return n**2

if __name__ == '__main__':
    p = Pool(2)
    apply_result = []
    for i in range(1, 10):
        obj = p.apply_async(task, args=(i, ))
        apply_result.append(obj)
    p.close()
    p.join()
    for obj in apply_result:
        print(obj.get())
--------------------------------------------------------------------------------
1112 worker process start do a task 
5776 worker process start do a task 
1112 worker process has done a task
1112 worker process start do a task 			# 注意每一个进程池中的工作进程的id
5776 worker process has done a task
5776 worker process start do a task 
1112 worker process has done a task
1112 worker process start do a task 
5776 worker process has done a task
5776 worker process start do a task 
1112 worker process has done a task
1112 worker process start do a task 
5776 worker process has done a task
5776 worker process start do a task 
1112 worker process has done a task
1112 worker process start do a task 
5776 worker process has done a task
1112 worker process has done a task
1
4
9
16
25
36
49
64
81

Process finished with exit code 0
```





> immediately     立马，立刻
> prevent            阻止
> submitted       屈服     
> garbage          垃圾  

- close()
	- Prevents any more tasks from being submitted to the pool
	- Once all the tasks have been completed the worker processes will exit.
	- Note:
		- join() method must be called after it
		- close()进制其他进程往进程池内额提交任务，而不是关闭进程池
		- apply_async()返回的appply_result是一个appply_result对象，也有join()方法，但是在调用p.join()之前必须先使用p.close()防止其他程序仍向进程池中提交进程

- terminate()
	- Stops the worker processes immediately without completing outstanding work. 
	- When the pool object is garbage collected terminate() will be called immediately
		- 当Pool被垃圾回收的时候，terminate被立即自动调用，为了防止留下僵尸进程

- join()
	- Wait for the worker processes to exit
		- 阻塞主进程直到进程池中的进程全部完成队列中的任务.
	-  **must call close() or terminate() before using join().**


- **Note**
	- apply_async()异步提交，主进程一旦执行完，进程池内的进程就得全部结束，所以要join()，如果主进程是一个死循环，就没有必要使用join()
	
> 异步提交：将主进程将任务提交到进程池的时候，不会去等待任务的结果，返回一个apply_result对象用来最后调取结果，主进程会继续往进程池中提交进程，进程池满了的时候就会等待进程池任务完成再提交


```python

# 对于apply()，它是同步提交的
from multiprocessing import Pool
import time
import os

def task(n):
    print('%s worker process start do a task '%os.getpid())
    time.sleep(1)
    print('%s worker process has done a task'%os.getpid())
    return n**2

if __name__ == '__main__':
    p = Pool(2)
    for i in range(10):
        # p.apply_async(task, args=(i, ))
        p.apply(task, args=(i, ))
    print('主进程')
--------------------------------
1492 worker process start do a task 
1492 worker process has done a task
9032 worker process start do a task 
9032 worker process has done a task
1492 worker process start do a task 
1492 worker process has done a task
9032 worker process start do a task 
9032 worker process has done a task
1492 worker process start do a task 
1492 worker process has done a task
9032 worker process start do a task 
9032 worker process has done a task
1492 worker process start do a task 
1492 worker process has done a task
9032 worker process start do a task 
9032 worker process has done a task
1492 worker process start do a task 
1492 worker process has done a task
9032 worker process start do a task 
9032 worker process has done a task
主进程
```



```python
# apply_async()
# 异步提交，主进程一旦执行完，进程池内的进程就得结束
from multiprocessing import Pool
import time
import os

def task(n):
    print('%s worker process start do a task '%os.getpid())
    time.sleep(1)
    print('%s worker process has done a task'%os.getpid())
    return n**2

if __name__ == '__main__':
    p = Pool(2)
    for i in range(10):
        p.apply_async(task, args=(i, ))
        # p.apply(task, args=(i, ))
    print('主进程')

-------------------------------------------------------------------
主进程

Process finished with exit code 0


```

- map(func, iterable[, chunksize])
	- Pool的map方法运行机制和python内置map函数运行机制相同。但是，Pool中的map方法会阻塞程序，直到返回任务结果（类似apply）。
	- func 就是要执行的任务对应的函数
	- iterable是一个可迭代对象，map函数可以将该对象中的值一个(chunk)个的作为func的参数传递给func处理
	- chunksize ----> The (approximate) size of these chunks can be specified by setting chunksize to a positive integer.
	- 注意：
		- 进程池的大小控制了可以同时并发的任务书，map返回会将iterable对象的元素依次遍历再传递给func

```python
import time
from multiprocessing import Pool

def task(i):
    time.sleep(1)
    print(i)

if __name__ == '__main__':
    p = Pool(5)
    p.map(task, range(20))
    p.close()
    p.join()

```

- map_async(func, iterable[, chunksize[, callback[, error_callback]]])
	- 类似apply_async, map_async方法是异步的提交func任务，当callback指向一个callable对象的时候，**map(func, iterable)的result封装到一个列表**中作为回调函数的参数，实现回调
	- 关于`error_callback`
		- 如果func在运行时出现错误，可以将错误这个exception object作为参数传递给error_callback的对象进行其他操作
	

```python
import time
from multiprocessing import Pool

def task(i):
    time.sleep(1)
    return  i

def f(res):
    print('callback result:', res)


if __name__ == '__main__':
    p = Pool(5)
    res = p.map_async(task, range(20), callback=f)
    print('res.get() = ',res.get())
    p.close()
    p.join()
# -----------------------------------------------------------------
callback result: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
res.get() =  [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19]

Process finished with exit code 0
```


```python
from multiprocessing import Pool

def f(i):
    return i / 0


def p(res):
    print(res)


def exp(exception):
    if isinstance(exception, ZeroDivisionError):
        print('handlers of Exception ')
        p.close()


if __name__ == '__main__':
    p = Pool()
    p.map_async(f, range(5), callback=p, error_callback=exp)
    p.close()
    p.join()

```

- 上面的map()和map_async()在targe func参数不止一个的时候明显就不再适用了，multiprocessing提供了下面这个方法
- startmap(func, iterable[, chunksize])
- startmap_async(func, iterable[, chunksize])
	- 作用和multiprocessing.map()与multiprocessing.map_async()一样，但是他可以为func传入多个参数，这些参数可以作为子序列存放在iterable对象中，在传递给func的时候会将子序列中的参数unpack拆包。
	- startmap也会阻塞程序直到返回func的结果
	- startmsp_async在提交完任务之后就直接返回一个result object，类似apply_async返回的apply_result对象一样，对于result对象，可以使用其get()方法获取返回值


- 使用map完成爬虫

```python
urls = [
    'https://www.baidu.com',
    'http://www.openstack.org',
    'https://www.python.org',
    'https://help.github.com/',
    'http://www.sina.com.cn/',
    'http://www.ziawang.com'
]

from multiprocessing import Pool
import requests


def get_page(url):
    response = requests.get(url)
    print(response.status_code)
    return response


def parser(res):
    filename = str(hash(res.url))
    with open(filename, 'w') as f:
        for item in res.header:
            f.write('%s : %s' % item)
        for chunk in res.iter_content(1024, decode_unicode='utf-8'):
            f.write(chunk)
        else:
            print(res.url, ' ........end.........')


if __name__ == '__main__':
    p = Pool(4)
    res = p.map_async(get_page, urls, callback=parser)
    p.close()
    p.join()
```

- 注意
	1. 进程池的大小对程序运行效率是有影响的，要耐心调试
	2. python中有两个能够用map函数完成并发的库
		1. `multiprocessing.Pool`
		2. `multiprocessing.dummy`   ，dummy是多进程模块的一个克隆文件，dummy使用的是线程

```python
# 使用multiprocessing.dummy
from multiprocessing.dummy  import Pool  as ThreadPool
p = ThreadPool()

```

 



## Pool methods待补充

- map(func, iterable, [chunksize])
- map_async(func, iterable[, chunksize[, callback[, error_callback]]])
- imap(func, iterable[, chunksize])
- imap_unordered(func, iterable[, chunksize])
- starmap(func, iterable[, chunksize])
- starmap_async(func, iterable[, chunksize[, callback[, error_back]]])

   