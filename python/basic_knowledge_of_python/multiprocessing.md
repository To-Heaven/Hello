# The Process Class
> spawned  大量生产的  
> processes are spawned by creating a Process object and then calling its start() method.  

- Note:
	- the `if __name__ == '__main__' `part is necessary


> semantic       语义，语法   
> daemon         守护天使   


## Process object
- multiprocessing.Process(group=None, target=None, name=None, args=(), kwargs={}, \*, daemon=None)
	- ***constructor should always be called with keyword arguments*
	- groups:
		- should always be None
	- target:
		-  target is the callable object to be invoked by the run() method
	-   name:
		-   process’s name.The name is a string used for identification purposes only
	-    args
		-   the argument tuple for the target invocation
	-    keargs
		-    a dictionary of keyword arguments for the target invocatio
	-    daemon	
		-     sets the process daemon flag to True or False.
		-     if None (the default), this flag will be inherited from the creating process.
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

- 当子进程调用start()方法时候，会将子进程的内存加载到内存，并且将父类的内存空间中的内容copy到自己内存空间中，这样子进程就会产生一个IO阻塞，操作系统就会调用其他进程去使用CPU

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
	- must be called at most once per process objec
	-  It arranges for the object’s run() method to be invoked in a separate process.


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

- terminate()
	- Terminate the process.
	- On Unix this is done using the SIGTERM signal; on Windows TerminateProcess() is used
	- **If this method is used when the associated process is using a pipe or queue then the pipe or queue  become corrupted and may become unusable by other process. Similarly, if the process has acquired a lock or semaphore etc. then terminating it is liable to cause other processes to deadlock.**


- is_alive()
	- Return whether the process is alive.
	- a process object is alive from the moment the start() method returns until the child process terminates.


## process_obj.variables
- pid
	- Return the process ID. Before the process is spawned, this will be None.
	- 使用起来比os.getpid()方便








