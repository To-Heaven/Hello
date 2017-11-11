# multiprocessing模块

## Prcoess Object
#### 创建Process
##### 通过实例化Process对象创建
- multiprocessing.Process(group=None, target=None, name=None, args=(), kwargs={}, \*, daemon=None)
	- **constructor should always be called with keyword arguments**实例化Process对象时，实参要以关键字行形式传参
	- groups:
		- should always be None。请忽视此参数
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

##### 通过创建Process子类创建
- 子类需要显示继承Process的初始化函数`__init__()`
- 将Process要执行的任务封装到`run()`方法中

```python
from multiprocessing import Process,cpu_count
import time


def mytimer(func):
    def inner(*args, **kwargs):
        start = time.time()
        ret = func(*args, **kwargs)
        print(f'COST: {time.time()-start}')
        return ret
    return inner


class MyProcess(Process):
    def __init__(self, num):
        super().__init__()
        self.num = num

    @mytimer
    def run(self):
        self.fibo(self.num)


    def fibo(self, n):
        if n < 2:
            return 1
        return self.fibo(n-1) + self.fibo(n-2)


if __name__ == '__main__':
    for i in range(cpu_count()):
        p = MyProcess(35)
        p.start()
```

#### Process的启动过程
1. Process对象调用`start()`方法，开始准备Process所需的资源，因IO阻塞被挂起
2. Process对象调用`run()`方法，进程任务开始进行。


#### Process对象的属性
- p.pid
	- 返回进程pid
	- Return the process ID. Before the process is spawned, this will be None.
- p.name
	- 进程的名称
- p.exitcode
	- 当进程运行时，该变量值为None
	- 当该变量值为-N时，表示被信号N结束
- p.authkey
	- 进程的身份验证键
	- **值为os.urandom()随机生成的32字符的字符串**
	- 用于为网络连接底层进程间通信提供安全，这类链接只有在相同身份验证键时才能成功

#### Process对象的方法
- p.run()
	- representing the process’s activity.
	- can override this method in a subclass.
		- invokes the callable object passed to the object’s constructor as the target argument
		- if any, with sequential and keyword arguments taken from the args and kwargs arguments, respectively.

- p.start()
	- start the process's activity
	- must be called at most once per process object
	-  It arranges（准备） for the object’s run() method to be invoked in a separate process.


- p.join([timeout])
	- Wait until the process terminates
	- blocks the calling process until the process whose join() method is called terminates – either normally or through an unhandled exception – or until the optional timeout occurs.
		- 这个方法会阻塞当前进程，直到
			- 进程正常结束
			- 出现异常
			- 超出timeout限制
	- **join()方法只能阻塞由start方法开启的进程，不能够阻塞住由run方法开启的进程**
		- 由run()直接开启的进程，没有分配到进程id。join()想要阻塞该进程先要通过进程id找到该进程，所以如果用run方法开启的该进程，就没法join
	- **start()开启之后的进程，再调用run()，该任务会再执行一次** 
	
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

- p.terminate()
	- Terminate the process.
	- **On Unix this is done using the SIGTERM signal; on Windows TerminateProcess() is used** Linux中是通过系统信号来只终结进程，Windows中是通过TerminateProcess()函数实现
	- **If this method is used when the associated process is using a pipe or queue then the pipe or queue  become corrupted and may become unusable by other process. Similarly, if the process has acquired a lock or semaphore etc. then terminating it is liable to cause other processes to deadlock.**
		- 如果被终结的进程有子进程，那么该子进程就会成为僵尸进程
		- 如果该进程任务使用着Queue队列或Pipe管道，终结该进程会导致队列或管道被破坏，其他进程就无法继续使用该队列或管道
		- 如果该进程中使用了锁，终止该进程可能会导致该锁无法释放，出现死锁



- p.is_alive()
	- Return True the process is alive.
	- a process object is alive from the moment the start() method returns until the child process terminates.
		- 操作系统关闭该进程需要时间，如果调用terminate()后立即调用is_alive()，可能会返回True


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





#### 注意事项
1. Windows系统中，将多进程的执行和创建过程放在`if __name__ == "__main__"`下
2. 再次强调：进程之间是一个个独立的内存空间，不能直接进行通信（数据传输和共享）
3. 子进程的启动过程中存在阻塞IO，因此在开启之后的一段时间内是内挂起的。
	1. 整个进程所需的资源
	2. Linux系统，子进程还会copy父进程的内存空间

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
```
 


## Lock Object
- Lock对象内部实现了上下文协议`__enter__()`和`__exit__()`。因此使用with语句操作Lock的加锁和释放是首选


#### 创建 Lock
- `mutex = Lock()`



#### Lock 对象的方法
- mutex.acquire()
	- acquire成功后，会返回以一个`True`
	- 上锁，保护数据直至锁被释放
- mutex.release()
	- 释放锁

```python
import json
import os
from multiprocessing import Process, Lock, process


def search():
    stock = json.load(open('stock'))
    print(f'last counts: {stock["count"]}')

def get_stock(mutex):
    with mutex:
        last_stock = json.load(open('stock'))
        if last_stock['count'] > 0:
            last_stock['count'] -= 1
            json.dump(last_stock, open('stock', 'w'))
            print(f'{os.getpid()} get one stock ,'
                  f' there is just {last_stock["count"]} '
                  f'stocks  remainder')

def task(mutex):
    search()
    get_stock(mutex)


if __name__ == '__main__':
    mutex = Lock()
    for i in range(10):
        p = Process(target=task, args=(mutex, ))
        p.start()
```

#### Lock和join()
- 



## Queue Object队列
- 队列由管道和锁/semphores实现，保证了数据的安全并且实现进程间通信


#### 创建Queue project
- Queue([maxsize])
	- Returns a process shared queue implemented using a pipe and a few locks/semaphores
	- When a process first puts an item on the queue a feeder thread is started which transfers objects from a buffer into the pipe.
		- 当maxxize不指定的时候，返回的Queue对象长度不限制 
#### Queue 对象方法
- **q.put(obj, block=True, timeout=None)**
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

- **q.get(block=True, timeout=None)**
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

- **q.close()**
	-** Indicate that no more data will be put on this queue by the current process**
		- 如果在close方法后继续执行put就会报错
- q.join_thread()
- q.cancel_join_thread()



## JoinableQueue
- JoinableQueue是Queue的子类。

#### 创建JoinableQueue
- `q = JoinableQueue([maxsize])`


#### Joinable对象方法
- **q.join()**
	- **Block until all items in the queue have been gotten and processed**.
	- The count of unfinished tasks goes up whenever an item is added to the queue.
	-  The count goes down whenever a consumer calls task_done() to indicate that the item was retrieved and all work on it is complete.
	-   **When the count of unfinished tasks drops to zero, join() unblocks.**


- **q.task_done()**
	- Note:
		- __Used by queue consumers__
	- Indicate that a formerly enqueued task is complete

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


## Manager Object
-没有实现锁，各个进程对于数据是竞争使用关系


#### 获取Manager对象
- `m = multiprocessing.Manager()`
	- return a multiprocessing.manages.SyncManager object which can be uesed for shaing objects between process
	- **the returned object corresponds to a spawned child process and has methods which will create shared objects and return corresponding proxies**
	- A manager returned by Manager() will support types **list, dict, Namespace, Lock, RLock, Semaphore, BoundedSemaphore, Condition, Event, Barrier, Queue, Value and Array**. 


#### Manager对象的方法
- 源码如下

```python
class SyncManager(multiprocessing.managers.BaseManager):
    def Barrier(self, parties, action=None, timeout=None):
        return threading.Barrier(parties, action, timeout)

    def BoundedSemaphore(self, value=None):
        return threading.BoundedSemaphore(value)

    def Condition(self, lock=None):
        return threading.Condition(lock)

    def Event(self):
        return threading.Event()

    def Lock(self):
        return threading.Lock()

    def Namespace(self):
        pass

    def Queue(self, maxsize=None):
        return queue.Queue()

    def RLock(self):
        return threading.RLock()

    def Semaphore(self, value=None):
        return threading.Semaphore(value)

    def Array(self, typecode, sequence):
        pass

    def Value(self, typecode, value):
        pass

    def dict(self, mapping_or_sequence):
        pass

    def list(self, sequence):
        pass
```


```python
from multiprocessing import Manager, Process


def modify(ns, lproxy, dproxy):
    ns.a **= 2
    lproxy.extend(['b', 'c'])
    dproxy['b'] = 0


if __name__ == '__main__':
    manager = Manager()
    
    ns = manager.Namespace()
    ns.a = 2

    lproxy = manager.list()
    lproxy.append('a')

    dproxy = manager.dict()
    dproxy['b'] = 2

    p = Process(target=modify, args=(ns, lproxy, dproxy))
    p.start()
    print(f'PID: {p.pid}')
    p.join()

    print(ns)
    print(ns.a)
    print(lproxy)
    print(dproxy)
```

## Pool object
- 控制同时并发的进程数


#### 创建 Pool object 对象
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

#### 工作进程
- ** worker process **
	- Worker processes within a Pool  live for the complete duration of the Pool’s work queue
		- 进程池中进行工作的进程，数量由processes指定，当其中一个id对应的进程完成一个队列中的任务之后，会以相同的id(即不会被回收释放)处理下一个任务


#### Pool 对象方法
- p.apply(func,[ args,[ kwargs]])
	- **It blocks until the result is ready**
		- func is only executed in one of the workers (worker processes) of the pool。同步提交任务时，即使存在多个工作进程，但是由于同步调用会阻塞直到结果返回，因此同步调用时，进程池中的工作进程是穿行运行的
	- parameters
		- func is the name of the function
		- args is a tuple of the arguments
			- 注意：args传参的时候虽然不用keyword方式传参，但是传入的参数必须放在元组中
		- kwds is the dict of the arguments  
	- Call func with arguments args and keyword arguments kwds
	- apply() <==> apply_async().get()
		-  apply_async() is better suited for work in parallel

```python
import requests
import os
from  contextlib import closing
from multiprocessing import Pool

urls = [
    'http://www.baidu.com',
    'http://www.icoolxue.com/album/show/123',
    'http://docs.jinkan.org/docs/flask/quickstart.html',
    'https://www.gitbook.com/book/chenryn/elk-stack-guide-cn/details',
    'https://www.baidu.com/link?url=COBehKD7HMbFNJ5KdS_YQ9TF-1GfsWozzSQLNTGnYQ9Zfpr6aNb5knuc9AucTMzz&wd=&eqid=d8ef4a19000323da0000000259d36158',
    'https://baike.baidu.com/item/AMQP/8354716?fr=aladdin',
    'https://baike.baidu.com/item/Erlang'
]


class MySpider:
    CHUNKSIZE = 1024


    def __init__(self, url):
        self.url = url
        response = requests.get(url)
        if response.status_code == requests.codes.ok:
            print(f'{response.url} ok')
            self.response = requests.get(url)

    def parser_to_file(self):
        import  time
        filename = str(hash(self.url))
        with closing(open(filename, 'wb')) as wf:
            time.sleep(3)
            for line in self.response.iter_content(chunk_size=self.CHUNKSIZE):
                wf.write(line)
            else:
                print(f'{os.getpid()} {self.url} parser OK , {len(self.response.content)}')


if __name__ == '__main__':
    p = Pool(4)
    for url in urls:
        spider = MySpider(url)
        p.apply(spider.parser_to_file)
```


- p.apply_async(func[, args[, kwds[, callback[, error_callback]]]])
	- func , args, kwds is memtioned above
	- apply_async的一个坑
		- 当传参错误时，比如传入参数多于被调函数形参数，程序不会报错，而是很恶心的结束进程
	- 参数
		- callback（回调函数，参考进程与线程）
			-  should be callable
			-  When the result becomes ready callback is applied to it, that is unless the call failed, in which case the error_callback is applied instead.
				-  当进程结果返回时，会自动调用回调函数并将结果作为参数传入，如果传递失败，就发送给error_callback指向的函数
		- error_callback
			-   should be a callable
			-   If the target function fails, then the error_callback is called with the exception instance
		 - 注意： callback应该被立刻调用，除非控制运算结果的线程被阻塞了
			 - Callbacks should complete immediately since otherwise the thread which handles the results will get blocked.

```python
# 一个坑
import requests
import os
from  contextlib import closing
from multiprocessing import Pool

urls = [
    'http://www.baidu.com',
    'http://www.icoolxue.com/album/show/123',
    'http://docs.jinkan.org/docs/flask/quickstart.html',
    'https://www.gitbook.com/book/chenryn/elk-stack-guide-cn/details',
    'https://www.baidu.com/link?url=COBehKD7HMbFNJ5KdS_YQ9TF-1GfsWozzSQLNTGnYQ9Zfpr6aNb5knuc9AucTMzz&wd=&eqid=d8ef4a19000323da0000000259d36158',
    'https://baike.baidu.com/item/AMQP/8354716?fr=aladdin',
    'https://baike.baidu.com/item/Erlang'
]


class MySpider:
    CHUNKSIZE = 1024

    def __init__(self, url):
        self.url = url

    def get_response(self):
        response = requests.get(self.url)
        print(response.status_code)
        if response.status_code == requests.codes.ok:
            print(f'{response.url} ok')
            return response

    def parser_to_file(self, res):
        self.response = res
        import time
        filename = str(hash(self.url))
        with closing(open(filename, 'wb')) as wf:
            time.sleep(3)
            for line in self.response.iter_content(chunk_size=self.CHUNKSIZE):
                wf.write(line)
            else:
                print(f'{os.getpid()} {self.url} parser OK , {len(self.response.content)}')


if __name__ == '__main__':
    p = Pool(4)
    pro_l = []

    for url in urls:
        spider = MySpider(url)
        p.apply_async(spider.get_response, url, callback=spider.parser_to_file)
        pro_l.append(p)
    p.close()
    p.join()

    for pro in pro_l:
        pro.join()
```

```python
# 正确的
import requests
import os
from  contextlib import closing
from multiprocessing import Pool

urls = [
    'http://www.baidu.com',
    'http://www.icoolxue.com/album/show/123',
    'http://docs.jinkan.org/docs/flask/quickstart.html',
    'https://www.gitbook.com/book/chenryn/elk-stack-guide-cn/details',
    'https://www.baidu.com/link?url=COBehKD7HMbFNJ5KdS_YQ9TF-1GfsWozzSQLNTGnYQ9Zfpr6aNb5knuc9AucTMzz&wd=&eqid=d8ef4a19000323da0000000259d36158',
    'https://baike.baidu.com/item/AMQP/8354716?fr=aladdin',
    'https://baike.baidu.com/item/Erlang'
]


class MySpider:
    CHUNKSIZE = 1024

    def __init__(self, url):
        self.url = url

    def get_response(self):
        response = requests.get(self.url)
        print(response.status_code)
        if response.status_code == requests.codes.ok:
            print(f'{response.url} ok')
            return response

    def parser_to_file(self, res):
        self.response = res
        import time
        filename = str(hash(self.url))
        with closing(open(filename, 'wb')) as wf:
            time.sleep(3)
            for line in self.response.iter_content(chunk_size=self.CHUNKSIZE):
                wf.write(line)
            else:
                print(f'{os.getpid()} {self.url} parser OK , {len(self.response.content)}')


if __name__ == '__main__':
    p = Pool(4)
    pro_l = []

    for url in urls:
        spider = MySpider(url)
        p.apply_async(spider.get_response, callback=spider.parser_to_file)
        pro_l.append(p)
    p.close()
    p.join()

    for pro in pro_l:
        pro.join()
```


- 回调函数
	-  一个进程运行完得到一个结果之后，将这个结果当作中间数据传递给另外一个函数进行处理
	-  **回调函数存在于主进程或调用回调函数的父进程中**
	-  使用进程池处理任务的时候，对于比较耗费时间的任务，放在进程池中，进行并发多进程处理任务，对于耗费时间较少并且需要对上述耗时任务结果进行处理的任务就需要使用回调函数



- p.map(func, iterable[, chunksize])
	- Pool的map方法运行机制和python内置map函数运行机制相同。对于一个`p = Pool(5)`的进程城，`p.map(func, iter)`会每次从iter中调用5个元素传入func中，并发的让工作进程处理
	- func 就是要执行的任务对应的函数
	- iterable是一个可迭代对象，map函数可以将该对象中的值一个(chunk)个的作为func的参数传递给func处理
	- chunksize ----> The (approximate) size of these chunks can be specified by setting chunksize to a positive integer.通过设置chunksize可以调整传入对象的顺序。如果设置为1，表示从第一个开始，步长为1，每次传入maxsize个元素。如果设置为2，，表示从第一个开始，步长为2，每次传入maxsize个元素
	- 注意：
		- 只要进程池没满，map就不断从iterable对象中取出元素去创建新进程。

```python
from multiprocessing import Pool
import time
import os


def task(i):
    print(f'{os.getpid()} begin')
    print(i)
    time.sleep(5)
    print(f'{os.getpid()} end')


if __name__ == '__main__':
    a = range(1, 20)
    p = Pool(5)
    p.map(task, a, chunksize=1)
    # p.map(task, a, chunksize=2)
    # p.map(task, a, chunksize=3)
```






- p.map_async(func, iterable[, chunksize[, callback[, error_callback]]])
	- 类似apply_async, map_async方法是异步的提交func任务，利用iterable对象中的元素创建进程后，不会等待调用的结果
	- 当callback指向一个callable对象的时候，**map(func, iterable)的result封装到一个列表**中作为回调函数的参数，实现回调
	- 关于`error_callback`
		- 如果func在运行时出现错误，可以将错误这个exception object作为参数传递给error_callback的对象进行其他操作


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
    print(res)
    filename = str(hash(res.url))
    with open(filename, 'w', encoding='utf-8') as f:
        for item in res.headers:
            f.write('%s' % item)
        for chunk in res.iter_content(1024, decode_unicode='utf-8'):
            f.write(chunk)
        else:
            print(res.url, ' ........end.........')


def parser_list(l):
    for res in l:
        parser(res)

if __name__ == '__main__':
    p = Pool(4)
    res = p.map_async(get_page, urls, callback=parser_list)
    p.close()
    p.join()
```


- p.starmap(func, iterable[, chunksize])
	- Like map() except that the elements of the iterable are expected to be iterables that are unpacked as arguments。如果func有多个参数，再使用map就不合适了。
	- Hence an iterable of [(1,2), (3, 4)] results in [func(1,2), func(3,4)]

```
from multiprocessing import Pool


def task(a, b):
    print(f'{a} {b}')


if __name__ == '__main__':
    d = [(i, j) for i in range(3) for j in range(10)]
    p = Pool(3)
    # p.map(task, d)
#      报错
    p.starmap(task, d)

```


- p.starmap(func, iterable[,chunksize[callback[error_back]]])
	- 异步提交任务，starmap是同步提交任务


- p.imap(func, iterable[, chunksize])
	-  作用同map，但是返回的是存放所有func结果的迭代器

```python
from multiprocessing import Pool


def task(i):
    return i * 2


if __name__ == '__main__':
    l = [i for i in range(20)]
    
    p = Pool(3)
    res = p.imap(task, l)
    for i in res:
        print(i)
    
    p.close()
    p.join()

```

- imap_unordered(func, iterable[,chunksize])

#### 使用multiprocess.dummy的正确姿势
- 我们有时候拿不准到底是使用多进程还是多线程来处理任务。处理这种情况，一个简单暴力的方法是使用这个模块实现'兼容模式'。看测试结果到底哪个更好就采用哪个
	- `multiprocessing.dummy`虽然是在多进程模块中的，但是它提供了和多线程基本相同的接口。
	- 官方原话
		- multiprocessing.dummy replicates the API of multiprocessing but is no more than a wrapper around the threading module.

```python
# from multiprocessing import Pool
from multiprocessing.dummy import Pool
```


- 