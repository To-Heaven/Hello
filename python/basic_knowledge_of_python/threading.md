## module functions
> dummy    仿制的， 挂名的，虚拟的
> exclude    不包括   


- threading.enumerate()
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


## two ways to create threads
- **Note**：
	- Once a thread object is created, its activity must be started by calling the thread’s start() method.
		- this invokes the run() method in a seprate thread of control  
	-  Once the thread’s activity is started, the thread is considered ‘alive’
		-  It stops being alive when its run() method terminates or by raising an unhandled exception
		-  threading.is_alive()
	- A thread has a name. The name can be passed to the constructor, and read or changed through the name attribute.
		- see  threading.getName()     obj.name  below
	- A thread can be flagged as a “daemon thread”
		- __The significance of this flag is that the entire Python program exits when only daemon threads are left__
		- the flag can be set through the daemon property or the daemon constructor argument.
	- main thread
		- corresponds to the initial thread of control in the Python program
	- **Note about the daemon thread**
		- __Daemon threads are abruptly stopped at shutdown. Their resources (such as open files, database transactions, etc.) may not be released properly__
		- If you want your threads to stop gracefully, make them non-daemonic and use a suitable signalling mechanism such as an Event.


1. passing a callable object to the constructor
- threading.Thread(target, args, kwargs, name, daemon)


2. overiding the run() method in a subclass
- **Note**:
	-  No other methods (except for the constructor) should be overridden in a subclass. In other words, only override the __init__() and run() methods of this class.（意思是除了构造函数和run()之外的其他方法都不应该被覆盖）






























- obj.name   和   obj.getName()区别

















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

