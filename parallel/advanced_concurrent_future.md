# Executor objects
- 一个抽象类，提供了异步调用的method。
	- ThreadPoolExecutor继承自Executor，实现了异步线程池
	- ProcessPoolExecutor继承自Executor，实现了异步线程池



## Module Functions
- concurrent.futures.wait(fs, timeout=None, return_when=ALL_COMPLETED)
- concurrent.futures.as_completed(fs, timeout=None)
	- 返回一个存放future对象的迭代器，由于这是模块级别的函数，因此可能包含不同的由executor和对应的fs(future sequence)返回的future对象，这个future对象是指已经完成的（complete  or  cancelled）



### executor.submit(func, *args, **kwargs)
- Schedules the callable, fn, to be executed as fn(*args **kwargs)
- **returns a Future object representing the execution of the callable**
	- 调用future对象的result方法可以阻塞当前进程直到返回结果
- submit向异步进程池提交任务之后，任务被进程池中指定个数的**工作进程**执行，当前进程不会等待结果，而是会继续提交下一个任务。因此开启一个异步进程池后，可以使用submit不断地向进程池中提交任务。
	- 使用future对象的回调函数来处理func执行的结果。
	- 传参时，不要传入整个序列，比如e.submit(func, (1, )) 是错误的，应该是e.submit(func, 1)，不能和Process实例化时参数传递弄混

```python
from concurrent.futures import ProcessPoolExecutor
import os
import time


def task():
    print('%s begin ' % os.getpid())
    time.sleep(1)
    return os.getpid()

if __name__ == '__main__':
    with ProcessPoolExecutor(2) as e:
        e.submit(task)
        e.submit(task)
        e.submit(task)
        e.submit(task)
        e.submit(task)
```


### map(func, *iterables, timeout=None, chunksize=1) 
- func is executed asynchronously and several calls to func may be made concurrently（同时开启多个异步进程）
- 返回值是一个迭代器
- 关于**chunksize**参数（对ThreadPoolExecutor没有用）
	- Using ProcessPoolExecutor with chunksize, this method chops iterables into a number of chunks which it submits to the pool as separate tasks. The  size of these chunks can be specified by setting chunksize to a positive integer. **For very long iterables, using a large value for chunksize can significantly improve performance compared to the default size of 1**. 
	- **With ThreadPoolExecutor, chunksize has no effect.**

```python
from concurrent.futures import ProcessPoolExecutor
import os
import time


def task(i):
    print('%s begin %s' % (os.getpid(), i))
    time.sleep(1)
    return os.getpid(), i

if __name__ == '__main__':
    with ProcessPoolExecutor(2) as e:
        res = e.map(task, range(10))
    for i in res:
        print(i)                            # 返回func执行结果
```


> chunksize 测试

```python
# chunksize 为1

```

```python
# chunksize 为10

```


### shutdown(wait=True)
- Signal the executor that it should free any resources that it is using when the currently futures are done executing（executor执行任务结束时，通知其回收资源）
	- 使用with避免忘记使用shutdown
	- **在一个submit提交的任务中调用一个executor或Future对象的方法可能会导致死锁**
		- Calling Executor or Future methods from a callable submitted to a ProcessPoolExecutor will result in deadlock.



## Future object
- future 对象由Executor.submit()创建

#### future.result(timeout=None)
- Return the value returned by the call. If the call hasn’t yet completed then this method will wait up to timeout seconds.
- If timeout is not specified or None, there is no limit to the wait time.
- 注意，如果在submit之后紧跟着调用该submit对象返回的result方法，会阻塞所在进程



#### add_done_callback(func)
- 回调函数
- Attaches the callable fn to the future. fn will be called, with the future as its** only argument**, when the future is cancelled or finishes running.
	- **注意**
		- 调用future对象的回调函数只能传入future一个参数
		- 如果callback调用时出现了异常，异常会被记录或忽略！！！！




#### exception(timeout=None)
- Return the exception raised by the call. If the call hasn’t yet completed then this method will wait up to timeout seconds.


