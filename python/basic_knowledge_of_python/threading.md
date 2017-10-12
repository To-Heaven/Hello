# threading 模块
## Module Level Functions
> dummy    仿制的， 挂名的，虚拟的
> exclude    不包括   


- threading.enum9erate()
	- Return a list of all Thread objects currently alive
	- the list includes:
		-  the main thread
		-  daemonic  thread
		-  dummy thread objects created by current_thread()
			-  excludes terminated threads and threads that have not yet been started.
			-  不包括terminated thread 和 尚未开始的thread 
- threading.active_count()
	- Return the number of Thread objects currently alive.


- threading.current_thread()
	- Return the current Thread object, corresponding to the caller’s thread of control.

```python
from threading import Thread, current_thread


if __name__ == '__main__':
    a = current_thread()
    print(a)
------------------------------------------------------
<_MainThread(MainThread, started 6896)>
```

- threading.get_indent()
	- Return the ‘thread identifier’ of the current thread
	-  a nonzero integer  which  has no direct meaning
	-  identifiers may be recycled when a thread exits and another thread is created.
		-  it is intended as a magic cookie to be used e.g. to index a dictionary of thread-specific data.


```python
from threading import Thread, get_ident


def task():
    print('%s running ....'%get_ident())

if __name__ == '__main__':
    for i in range(10):
        t = Thread(target=task, name=str(i))  
        t.start()
--------------------------------------------------------------
2324 running ....
8580 running ....
4236 running ....
7012 running ....
6936 running ....
1796 running ....
4680 running ....
8864 running ....
7208 running ....
3284 running ....

Process finished with exit code 0
```


- threading.main_thread()
	- Return the main Thread object.
	-  the main thread is the thread from which the Python interpreter was started.


> trace   踪迹， 痕迹   
> stack   层积    



- threading.settrace(func)
	- Set a trace function for all threads started from the threading module.
	- The func will be passed to sys.settrace() for each thread, before its run() method is called.

- threading.setprofile(func)
	- Set a profile function for all threads started from the threading module.
	-  The func will be passed to sys.setprofile() for each thread, before its run() method is called.


```python
待补充


```


- threading.TIMEOUT_MAX
	- The maximum value allowed for the timeout parameter of blocking functions 
		- Lock.acquire()
		- RLock.acquire()
		- Condition.wait()
	- raise a OverflowError  while specifying a timeout greater than this value



> flag     标识    
> significance      重要性   
> abruptly         突然地   


## Thread_Local Data（单个线程私有的数据）
- Thread-local data is data whose values are thread specific
- values will be different for separate threads
	- 当进行多线程任务的同时，又需要每一个线程保存一个单独的数据供当前线程操作的时候，使用这种方法简单有效。
		- ps：多线程下载，对每个线程下载的数据做不同的处理
	- 实现这种功能有很多方法，比如实例化一个dict对象，以线程名作为key，对应的数据作为value，以达到相同效果，但明显没有这种方法方便灵巧 

```python
from threading import Thread, local, get_ident


class Widge:
    pass


def test():
    local_data = local()
    local_data.x = 1

    def thread_func():
        print(f'has x in new thread : {hasattr(local_data, "x")}')
        local_data.x = 2
        print(f'has x in new thread : {hasattr(local_data, "x")}')

    t = Thread(target=thread_func)
    t.start()
    t.join()
    print(f'x in pre thread is {local_data.x}')


if __name__ == '__main__':
    test()
```


## two ways to create threads

1. passing a callable object to the constructor
- threading.Thread(group=None, target=None, name=None, args=(), kwargs={}, *, daemon=None)
	- **This constructor should always be called with keyword arguments**
	- parameters:
		- group:should always be None
		- target:the callable object to be invoked by the run() method
		- name:the thread name. 
			- By default, a unique name is constructed of the form “Thread-N” where N is a small decimal number
		- args: the argument tuple for the target invocation. Defaults to ()
		- kwargs:a dictionary of keyword arguments for the target invocation. Defaults to {}


2. overiding the run() method in a subclass
- **Note**:
	-  No other methods (except for the constructor) should be overridden in a subclass. In other words, only override the __init__() and run() methods of this class.（意思是除了构造函数和run()之外的其他方法都不应该被覆盖）

```python
from threading import Thread

class MyThread(Thread):
    def __init__(self, name):
        super().__init__()
        self.name = name

    def run(self):
        print('当调用线程的start方法的时候，自动调用run()方法')
        self.f()
        self.g()

    def f(self):
        pass
    def g(self):
        pass

m = MyThread('ziawang')
m.start()
-------------------------------------------------------------------
当调用线程的start方法的时候，自动调用run()方法

Process finished with exit code 0

```


- **Note**：
	- Once a thread object is created, its activity must be started by calling the thread’s start() method.
		- this invokes the run() method in a seprate thread of control  
		- <参考multiprocessing.join()中对start()和run()开启进程的区别>
	-  Once the thread’s activity is started, the thread is considered ‘alive’
		1. It stops being alive when its run() method terminates or by raising an unhandled exception
		2. threading.is_alive()
	- A thread has a name. The name can be passed to the constructor, and read or changed through the name attribute.
		- see  threading.getName()     obj.name  below
	- A thread can be flagged as a “daemon thread”
		1. __The significance of this flag is that the entire Python program exits when only daemon threads are left__
		2. the flag can be set through the daemon property or the daemon constructor argument.
	- main thread
		- corresponds to the initial thread of control in the Python program
	- **Note about the daemon thread**
		1. __Daemon threads are abruptly stopped at shutdown. Their resources (such as open files, database transactions, etc.) may not be released properly__
		2. If you want your threads to stop gracefully, make them non-daemonic and use a suitable signalling mechanism such as an Event.




## variables  of  Thread objects
- t.name
	- a string  used for identification purposes only
- t.daemon
	- A boolean value indicating whether this thread is a daemon thread (True) or not (False)
	- Note:
		1. must be set before start() is called, otherwise RuntimeError is raised. 
		2. all threads created in the main thread default to daemon = False. because the main thread is not a daemon thread
- t.ident
	- The ‘thread identifier’ of this thread or None if the thread has not been started
	- Thread identifiers may be recycled when a thread exits and another thread is created
		- 所以当一个进程结束之后，这个进程曾使用的ident时可能会被继续使用的

> arrange     准备    
> thread  of  control     主线程（控制线程）    
> separate      分开的    

## Methods of Thread objects
- t.start()
	- Start the thread’s activity.
	- Note:
		1.  It must be called at most once per thread object.每一个线程都要调用start之后才能启动
		2.  It arranges for the object’s run() method to be invoked in a separate thread of control.
		3.  **raise a RuntimeError if called more than once on the same thread object.**

> respectively      分别的    

- t.run()
	- Method representing the thread’s activity . 即在run方法中实现了这个线程包含的任务
	- Note:
		1.  **override this method in a subclass**
		2.  invokes the callable object passed to the object’s constructor as the target argument.可调用对象会被run传递给创造线程时的参数target
- t.join(timeout=None)
	- Wait until the thread terminates
	- Note:
		- blocks the calling thread until the thread whose join() method is called terminates
		- what's the mean 'terminate'
			1. normally,target对应的任务正常执行完毕
			2. through an unhandled exception 异常
			3. until the optional timeout occurs.超时
	- *timeout*
		-  when the timeout argument is present and not None, it should be a floating point number specifying a timeout for the operation in seconds 
		-  When the timeout argument is not present or None, the operation will block until the thread terminates.

- t.getName()
	- return the name of thread
- t.setName()
	- set the name of a thread
- t.isDaemon()
	- used as the variable of daemon
- t.setDaemon()
	- set a thread to be a daemon or not 
- t.is_alive()
	- return True if the thread is still alive
	- what's thw mean 'alive'?
		- thread is alive before the run() method starts until just after the run() method terminates. 
	-  Point:**threading.enumerate()**


## The Global Interpreter Lock
- **this section is write for Cpython interpreter**
- Note:
	1. only one thread executes Python bytecode at a time.
	2. making the object model (including critical built-in types such as dict) implicitly safe against concurrent access
	3. threading is still an appropriate model if you want to run multiple I/O-bound tasks simultaneously
	4. If you want your application to make better use of the computational resources of multi-core machines, you are advised to use multiprocessing or concurrent.futures.ProcessPoolExecutor


## Lock Objects
- Lock.acquire(blocking=True, timeout=-1)
	- blocking
		- True:   block until the lock is unlocked and return True
		- False: Do not block
	- timeout
		-  block for at most the number of seconds specified by timeout  as long as the lock cannot be acquired
		-  A timeout argument of -1 specifies an unbounded wait
		-  注意，当block为False的时候，即使指定了timeout，该参数也将被忽略

> proceed      收获，获取    

- Lock.release()	
	- Release a lock
	- **When invoked on an unlocked lock, a RuntimeError is raised.**
	- If any other threads are blocked waiting for the lock to become unlocked, allow exactly one of them to proceed.



## RLock  Objects  递归锁
- 请看在[进程与线程原理](http://www.ziawang.com/python/basic_knowledge_of_python/processes_and_threads.html)中的解释
- Rlock.acquire(blocking=True, timeout)
- Rlock.release()


## Condition  Objects 
<br>



## Semaphore   Objects 信号量（联锁）
> Semaphore object 本质上就是多个锁，用来控制最大线程数    
> Semaphore  实现了控制多个线程共享同一段数据。而递归锁则是让一个线程能独享多段共享资源    

- a semaphore object manages a counter which is decreamented by each acquire() call and incremented by each release() call 
	- this counter can never  below zero
	- when acquire() finds that it is zero, it blocks, waiting until some other thread calls release().
- threading.Semaphore(value=1)
	- return a semphore object 
	- value is the counter , if the value given is less than 0, ValueError is raised

## Semaphore Objects Methods
- s.acquire(blocking=True, timeout=None)
	-  if the counter is larger than zero on entry, decrement it by one and return immediately
	-  If it is zero on entry, block, waiting until some other thread has called release() to make it larger than zero
	-  his is done with proper interlocking so that if multiple acquire() calls are blocked, release() will wake exactly one of them up（这个acquire会将多个线程联锁，等价于多个thread.acquire()，release()只能给一个线程解锁一次）

- s.release()
	- incrementing the internal counter by one, means that release a semaphore 

```python

from threading import Semaphore, Thread, get_ident
import time

def task():
    with mutex_semaphore:				# context management protocol
        print('%s get acquire '%get_ident())
        time.sleep(1)
        print('%s release '%get_ident())

if __name__ == '__main__':
    mutex_semaphore = Semaphore(3)
    t_l = []
    for i in range(10):
        t = Thread(target=task)
        t_l.append(t)
        t.start()
    for t in t_l:
        t.join()
    print('主线程其他代码')

------------------------------------------------------------------------------
4116 get acquire 
6180 get acquire 
13652 get acquire 
4116 release 
4636 get acquire 			# 每释放一个锁，counter计数加1， 就可以再新建一个线程，但是当counter为0的时候，就阻塞到counter不为0才能新建线程
13652 release 
6180 release 	
11568 get acquire 
7300 get acquire 
4636 release 
4440 get acquire 
7300 release 
11568 release 
11976 get acquire 
1448 get acquire 
4440 release 
25660 get acquire 
1448 release 
11976 release 
25660 release 
主线程其他代码

Process finished with exit code 0
```

> mechanism     机制    



## Event Objects  ——the simplest mechanisms for commucation between threads 
- one thread signals an event and other threads wait for it

- threading.Event()
	- return an event object manage a flag 
	- flag set to True:
		- set()
	- flag set to False:
		- clear()
	- wait()
		- blocks until the flag is True  

- **Methods of Event object**
- e.set()
	- set the flag to True 
	- All threads waiting for it to become true are awakened
	- **Threads that call wait() once the flag is true will not block at all.**

- e.is_set()
	- return True if the flag is True

- e.clear()
	- Reset the internal flag to false

- e.wait(timeout=None)
	- return True, if and only if the flag hash been set to True
	- Block until the internal flag is true
	- timeout argument should be a floating point number specifying a timeout for the operation in seconds 
 


模拟连接mysql服务端的事件同步
```python
from threading import Event, Thread, get_ident
import time


def connect_mysql():
    count = 1
    while not e.is_set():
        if count > 3:
            raise ConnectionError('%s请求次数过多，请稍后再尝试链接'%get_ident())
        print('%s 尝试第%s次连接. . . '%(get_ident(), count))
        count += 1
        e.wait(1)
    print('%s连接成功'%get_ident())

def start_mysql():
    print('正在启动mysql. . . ')
    time.sleep(3)
    e.set()


if __name__ == '__main__':
    e = Event()
    for i in range(3):
        t_conn = Thread(target=connect_mysql)
        t_conn.start()
    t_start = Thread(target=start_mysql)
    t_start.start()

```





## context management prorocol
- use the 'with' statement with the object which has  acquire() and release() methods in this medule 
	-  The acquire() method will be called when the block is entered
	-  release() will be called when the block is exited.
 

```python
def task():
    with mutex:
        print('%s get acquire '%get_ident())
        time.sleep(1)
        print('%s release '%get_ident())


if __name__ == '__main__':
    mutex = Lock()
    t = Thread(target=task)
    t.start()
    t.join()
    print('主线程其他代码')

```

上述代码等价于

```python
from threading import Thread, Lock, get_ident
import time

def task():
    mutex.acquire()
    try:
        print('%s get acquire '%get_ident())
        time.sleep(1)
        print('%s release '%get_ident())
    finally:
        mutex.release()


if __name__ == '__main__':
    mutex = Lock()
    t = Thread(target=task)
    t.start()
    t.join()
    print('主线程其他代码')

```


> simplified    使...简单    

## Timer object
- Timer(interval, function, args=None, kwargs=None)
	- interval:time to be passed before the function executed
	- function
		- run it  after interval seconds have passed.
		- args: the function's position arguments
		- kwargs: the function's keyword arguments
	- Note:
		-   If args is None (the default) then an empty list will be used. If kwargs is None (the default) then an empty dict will be used.


- start()
	- start the timer  

- cancel()
	- cancel the timer
		- This will only work if the timer is still in its waiting stage.
		- 在计时范围内有效


```python
# 不cancel()
import time
from threading import Timer

def f():
    print('time over')

my_timer = Timer(5, f)
my_timer.start()
---------------------------------------------------------
time over
```


```python
# 计时期间cancel()
import time
from threading import Timer

def f():
    print('time over')

my_timer = Timer(5, f)
my_timer.start()
time.sleep(4)
my_timer.cancel()

```

