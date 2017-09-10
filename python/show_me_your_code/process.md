# 进程

## multiprocessing.Process创建进程的两种方式（简单爬虫为例）
- 使用multiprocessing.Process()创建进程

```python

import requests
from multiprocessing import Process, Lock
import os

urls = [
    'http://www.ziawang.com/',
    'https://dev.mysql.com',
    'https://coolshell.cn/',
    'https://www.joinquant.com',
    'http://www.python-requests.org'
]


def get_page(url):
    response = requests.get(url)
    if response.status_code == requests.codes.ok:
        return response


def parser_page(url):
    response = get_page(url)
    filename = str(hash(response.url))
    with open(filename, 'wb') as fb:
        headers = response.headers
        fb.write(bytes(str(headers), encoding='utf-8'))
        for chunk in response.iter_content(1024):
            fb.write(chunk)
        else:
            print('%s :parser successful'%response.url)


if __name__ == '__main__':
    p_l = []
    for i in range(len(urls)):
        p = Process(target=parser_page, args=(urls[i],))
        p_l.append(p)
        p.start()
    for p in p_l:
        p.join()
    print('任务完成')


```

- 创建Process子类的方法实现多进程

```python
import requests
from multiprocessing import Process


class Mylittlespider(Process):
    parser_page_per_size = 1024
    ecoding = 'utf-8'

    def __init__(self, url):
        super().__init__()
        self.response = requests.get(url)
        if not self.response:
            raise TimeoutError('request failed')

    def run(self):
        self.parser_page()

    def parser_page(self):
        filename = str(hash(self.response.url))
        headers = self.response.headers
        with open(filename, 'wb') as fb:
            for item in headers.items():
                fb.write(bytes('%s : %s \n' % item, encoding=self.ecoding))
            for chunk in self.response.iter_content(self.parser_page_per_size):
                fb.write(chunk)
            else:
                print('%s parser successful ' % self.response.url)


urls = [
    'http://www.ziawang.com/',
    'https://dev.mysql.com',
    'https://coolshell.cn/',
    'https://www.joinquant.com',
    'http://www.python-requests.org'
]

if __name__ == '__main__':
    for url in urls:
        p = Mylittlespider(url)
        p.start()

```

>  但是这种爬虫方式太低效了，每一个进程内是存在I/O阻塞（网络IO和文件读写IO）的，并且进程内的过个任务之间是串行的，每一个parser方法都必须等到get_page之后才能运行，使用进程池看看


## multiprocessing.Pool实现多进程

```python
from multiprocessing import Pool
import requests


class Mylittlespider:
    parser_page_per_size = 1024
    encoding = 'utf-8'

    def __init__(self, url):
        self.response = requests.get(url)

    def get_page(self):
        if self.response.status_code == requests.codes.ok:
            return {'url':self.response.url, 'text':self.response.text}

    def parser_page(self, args):
        filename = str(hash(args['url']))
        headers = self.response.headers
        with open(filename, 'wb') as fb:
            for item in headers.items():
                fb.write(bytes('%s: %s \n'%item, encoding=self.encoding))
            for chunk in self.response.iter_content(self.parser_page_per_size):
                fb.write(chunk)
            else:
                print('%s end. . .'%args['url'])

if __name__ == '__main__':
    p = Pool(2)
    urls = [
        'http://www.ziawang.com/',
        'https://dev.mysql.com',
        'https://coolshell.cn/',
        'https://www.joinquant.com',
        'http://www.python-requests.org'
    ]
    for url in urls:
        m = Mylittlespider(url)
        res = p.apply(m.get_page)
        m.parser_page(res)
        print('all tasks have been done !')
```


- 为了提示apply方法传参时，args的传值方式（元组），改成这样


```python
class Mylittlespider:
    parser_page_per_size = 1024
    encoding = 'utf-8'


    def get_page(self, url):
        self.response = requests.get(url)
        if self.response.status_code == requests.codes.ok:
            return self.response

    def parser_page(self, response):
        filename = str(hash(response.url))
        headers = response.headers
        with open(filename, 'wb') as fb:
            for item in headers.items():
                fb.write(bytes('%s: %s \n'%item, encoding=self.encoding))
            for chunk in response.iter_content(self.parser_page_per_size):
                fb.write(chunk)
            else:
                print('%s end. . .'%response.url)

if __name__ == '__main__':
    p = Pool(2)
    urls = [
        'http://www.ziawang.com/',
        'https://dev.mysql.com',
        'https://coolshell.cn/',
        'https://www.joinquant.com',
        'http://www.python-requests.org'
    ]
    for url in urls:
        m = Mylittlespider()
        res = p.apply(m.get_page, (url, ))
        m.parser_page(res)
        print('all tasks have been done !')
```

- 然而，apply同步调用任务的方式，导致进程之间其实是串行运行的。使用apply_async试试看
	- apply_async即异步调用任务
	- 所谓同步，当发出一个功能调用时，在没有得到结果之前，该调用的结果不会返回，需要等待最终要返回的结果
	- 所谓异步，当发出一个功能调用时，调用者不会立即得到调用的结果，当该异步功能完成之后，通过状态、通知或回调来通知调用者

```python
class Mylittlespider:
    parser_page_per_size = 1024
    encoding = 'utf-8'


    def get_page(self, url):
        self.response = requests.get(url)
        if self.response.status_code == requests.codes.ok:
            return self.response

    def parser_page(self, response):
        filename = str(hash(response.url))
        headers = response.headers
        with open(filename, 'wb') as fb:
            for item in headers.items():
                fb.write(bytes('%s: %s \n'%item, encoding=self.encoding))
            for chunk in response.iter_content(self.parser_page_per_size):
                fb.write(chunk)
            else:
                print('%s end. . .'%response.url)

if __name__ == '__main__':
    p = Pool(2)
    urls = [
        'http://www.ziawang.com/',
        'https://dev.mysql.com',
        'https://coolshell.cn/',
        'https://www.joinquant.com',
        'http://www.python-requests.org'
    ]
    for url in urls:
        m = Mylittlespider()
        p.apply_async(m.get_page, (url, ), callback=m.parser_page)
    p.close()	# 进程池join()之前必须要有close()，防止其他程序在join过程中继续向进程池提交任务
    p.join()	# 真正的需求是实现爬取页面异步提交任务，实现并发，因此需要阻塞住主进程，等所有进程结束，才开始主进程的其他任务
    print('all tasks have been done !')

```

## multiprocessing.Process实现多进程模拟抢票

```python
import json
from multiprocessing import Process, Lock


class StockProcess(Process):
    filename = 'stock'

    def __init__(self):
        super().__init__()
        self.mutex = Lock()

    def run(self):
        self.last_count()
        self.mutex.acquire()
        self.get_stock()
        self.mutex.release()

    def last_count(self):
        last_count = json.load(open(self.filename))['count']
        print('%s surplus of stock'%last_count)
        return last_count

    def get_stock(self):
        current_stock = json.load(open(self.filename))
        if current_stock['count'] > 0:
            current_stock['count'] -= 1
            json.dump(current_stock, open(self.filename, 'w'))
            print('%s get stock successful'%self.pid)

if __name__ == '__main__':
    d = {'count': 100}
    json.dump(d, open('stock', 'w'))
    for i in range(150):
        s = StockProcess()
        s.start()

```

- 如果写成这样，结果就不同了

```python
import json
from multiprocessing import Process, Lock

d = {'count': 100}
json.dump(d, open('stock', 'w'))

class StockProcess(Process):
    filename = 'stock'

    def __init__(self):
        super().__init__()
        self.mutex = Lock()

    def run(self):
        self.last_count()
        self.mutex.acquire()
        self.get_stock()
        self.mutex.release()

    def last_count(self):
        last_count = json.load(open(self.filename))['count']
        print('%s surplus of stock'%last_count)
        return last_count

    def get_stock(self):
        current_stock = json.load(open(self.filename))
        if current_stock['count'] > 0:
            current_stock['count'] -= 1
            json.dump(current_stock, open(self.filename, 'w'))
            print('%s get stock successful'%self.pid)

if __name__ == '__main__':
    for i in range(150):
        s = StockProcess()
        s.start()

```

## 生产者与消费者模型
- 使用multiprocessing.JoinableQueue

```python
from multiprocessing import Manager, Process


class Producer(Process):
    def __init__(self, q):
        super().__init__()
        self.q = q

    def run(self):
        self.produce()

    def produce(self):
        for i in range(10):
            self.q.put(i)
            print('produce %s ' % i)
        self.q.join()


class Customer(Process):
    def __init__(self, q):
        super().__init__()
        self.q = q

    def run(self):
        self.custom()

    def custom(self):
        while True:
            res = self.q.get()
            print('custom %s' % res)
            self.q.task_done()


if __name__ == '__main__':
    with Manager() as m:
        q = m.JoinableQueue(3)
        p = Producer(q)
        c = Customer(q)
        c.daemon = True
        p.start()
        c.start()
        p.join()
        print('主进程')

```

- 如果这样写会报错

```python
from multiprocessing import Manager, Process


class Producer:
    def __init__(self, q):
        self.q = q

    def run(self):
        self.produce()

    def produce(self):
        for i in range(10):
            self.q.put(i)
            print('produce %s ' % i)
        self.q.join()


class Customer:

    def __init__(self, q):
        self.q = q


    def custom(self):
        while True:
            res = self.q.get()
            print('custom %s' % res)
            self.q.task_done()


if __name__ == '__main__':

    with Manager() as m:
        q = m.JoinableQueue(3)
        p = Producer(q)
        c = Customer(q)
        p_process = Process(target=p.produce)
        c_process = Process(target=c.custom)
        p_process.start()
        c_process.start()
        print('主进程')

```