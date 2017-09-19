# concurrent 模块
[concurrent源码](https://github.com/python/cpython/blob/3.6/Lib/concurrent/futures/process.py)


## Executeor objects
- 一个抽象类，提供了异步调用的method。
	- ThreadPoolExecutor
	- ProcessPoolExecutor


## Methods For Asynchronous
- e.submit(func, *args, **kwargs)
	-  调用func，执行func(*args, **kwargs)
	-  返回值为一个Futures对象，可以通过Futures对象的result方法获取结果
	-  注意：
		1. 最好使用with上下文来操作executor对象
		2. 传参时，不要传入整个序列，比如e.submit(func, (1, )) 是错误的，应该是e.submit(func, 1)，不能和Process实例化时参数传递弄混

```python
with ProcessPoolExecutor as executor:
	future = executor.submit(max, 1, 2, 3)
	print(future.result())

```

- e.map(func, *iterable, timeout=None, chunksize=1)
	- 可以将iterable对象中的元素作为参数传递给func调用
	- 返回值是一个生成器
	- 注意：
		- __e.map()可以实现多个任务并发执行， 并发执行的数目取决于实例化得到e时的参数配置__

```python
from concurrent.futures import ThreadPoolExecutor
import  time


def f(i):
    time.sleep(1)
    print(i, end=',', flush=True)

if __name__ == '__main__':
    with ThreadPoolExecutor(3) as executor:
        l = list(range(10))
        executor.map(f, l)

```



```python
urls = [
    'https://www.baidu.com',
    'http://www.openstack.org',
    'https://www.python.org',
    'https://help.github.com/',
    'http://www.sina.com.cn/',
    'http://www.ziawang.com'
]
import requests
from concurrent.futures import ThreadPoolExecutor


def get_page(url):
    response = requests.get(url)
    print(response.url, response.status_code)


if __name__ == '__main__':
    with ThreadPoolExecutor(6) as executor:
        res = executor.map(get_page, urls)
        print(type(res))

```


```python
class Mylittlespider:
    chunksize = 1024

    def parser(self, url):
        self.response = requests.get(url)
        print('%s begin parser'%self.response.url)
        filename = str(hash(self.response.url))
        with open(filename, 'w') as fw:
            for item in self.response.headers.items():
                fw.write('{} : {} \n'.format(*item))
            for chunk in self.response.iter_content(self.chunksize,
                                                    decode_unicode='utf-8'):
                fw.write(chunk)
            else:
                print('%s complete'%self.response.url)

if __name__ == '__main__':
    with ThreadPoolExecutor(3) as executor:
        spider = Mylittlespider()
        executor.map(spider.parser, urls)

```

```python
import requests

urls = [
    'https://www.baidu.com',
    'http://www.openstack.org',
    'https://www.python.org',
    'https://help.github.com/',
    'http://www.sina.com.cn/',
    'http://www.ziawang.com'
]

class Mylittlespider:
    chunksize = 1024
    charset = 'utf-8'

    def __init__(self, url):
        self.url = url
        self.response = requests.get(url)
        print(self.url, self.response.status_code)
        self.parser()
        if self.response != requests.codes.ok:
            raise ConnectionError('连接失败')


    def parser(self):
        print('%s begin parser'%self.response.url)
        filename = str(hash(self.url))
        with open(filename, 'w') as fw:
            for item in self.response.headers.items():
                fw.write('{} : {} \n'.format(*item))
            for chunk in self.response.iter_content(self.chunksize,
                                                    decode_unicode=self.charset):
                fw.write(chunk)
            else:
                print('%s complete'%self.url)

if __name__ == '__main__':
    with ThreadPoolExecutor(3) as executor:
        executor.map(lambda url:Mylittlespider(url), urls)

```

- e.shutdown(wait=True)
	- 执行此方法会向当前运行的executors发送信号
		- 如果wait为True，程序会等待这个任务完成，当这些executor执行完任务之后会释放自身的资源
		- 如果wait为False，程序不会等待任务完成，而是会立即返回（不建议改为False）
	- 如果在此方法之后调用e.submit()或e.map()会引发RuntimeError
	- **实际上，应首选with语句，而不是使用此方法**
	

## ThreadPoolExecutor Objects
- concurrent.futures.ThreadPoolExecutor(max_workers=None, thread_name_prefix='')
	- 如果max_works没有指定，默认为计算机的CPU数量（和ProcessPoolExecutor相同）
	- thread_name 是线程池中worker（工作线程）的名称，为了方便解决bug，ProcessPoolExecutor没有此参数
- 一个能够异步调用的线程池
- 注意，**在一个submit提交的任务中调用一个executor或Future对象的方法可能会导致死锁**
	- 官方这样解释Calling Executor or Future methods from a callable submitted to a ProcessPoolExecutor will result in deadlock.
	

```python
# 互相等待对方的Futures object
from concurrent.futures import ThreadPoolExecutor
import time


def wait_for_a():
    time.sleep(1)
    print(b.result())


def wait_for_b():
    print(a.result())

executor = ThreadPoolExecutor(2)
a = executor.submit(wait_for_a)
b = executor.submit(wait_for_b)

```

```python
# 在一个线程/进程中开启了另一个线程/进程
from concurrent.futures import ThreadPoolExecutor


def wait_for_future():
    f = executor.submit(pow, 2, 3)
    print(f.result())


executor = ThreadPoolExecutor(10)
executor.submit(wait_for_future)

```

- [一个关于map的问题](https://stackoverflow.com/questions/20838162/how-does-threadpoolexecutor-map-differ-from-threadpoolexecutor-submit)




```python
import concurrent.futures
import urllib.request

URLS = [
    'http://www.ziawang.com',
    'https://www.baidu.com',
    'https://www.python.org'
]

def get_page(url, timeout):
    with urllib.request.urlopen(url) as response:
        return response.read()


with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
    future_to_url = {executor.submit(get_page, url, 60): url for url in urls}
    # print(future_to_url)
    for future in concurrent.futures.as_completed(future_to_url):
        url = future_to_url[future]
        try:
            data = future.result()
        except Exception as e:
            print('%s generated a exception : %s' % (url, e))
        else:
            print('%r page is %d bytes' % (url, len(data)))


```



## ProcessPoolExecutor Objects
- 一个异步调用的进程池

- 注意，**在一个submit提交的任务中调用一个executor或Future对象的方法可能会导致死锁**
	- 官方这样解释Calling Executor or Future methods from a callable submitted to a ProcessPoolExecutor will result in deadlock.

## Future Objects
- future 对象由executor.submit()方法返回
- future.result(timeout)
	- 返回e.submit(func， *args)中func的调用结果
	- 注意：
		- 如果调用此方法时，func的调用还没结束，就会阻塞进程/线程，直到调用结束返回结果
		- 如果指定了timeout参数的值，当在指定timeout时间范围内没有返回调用结果时，会触发TimeoutError
- future.add_done_callback(func)
	- 回调函数，会在将future对象作为参数传递给func

## Module Functions
- concurrent.futures.wait(fs, timeout=None, return_when=ALL_COMPLETED)
- concurrent.futures.as_completed(fs, timeout=None)
	- 返回一个存放future对象的迭代器，由于这是模块级别的函数，因此可能包含不同的由executor和对应的fs(future sequence)返回的future对象，这个future对象是指已经完成的（complete  or  cancelled）




