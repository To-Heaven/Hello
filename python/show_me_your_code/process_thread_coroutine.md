# 进程、线程、协程
## socket通讯
### multiprocessing.Process多进程并发实现服务端和客户端socket简单通信

- 屌丝版

```python
# 服务端
from socket import *
from multiprocessing import Process


def talk(conn, client_addr):
    while True:
        try:
            print('..........')
            msg = conn.recv(1024)
            print('received from  %s' % client_addr[0])
            if not msg: break
            conn.send(msg.upper())
        except Exception as e:
            print(e)
            break
    conn.close()


def server(ip, port):
    server_side = socket(AF_INET, SOCK_STREAM)
    server_side.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
    server_addr = ip, port
    server_side.bind(server_addr)
    server_side.listen(5)
    conn, client_addr = server_side.accept()
    return conn, client_addr


if __name__ == '__main__':
    while True:
        conn, client_addr = server('127.0.0.1', 8080)
        p = Process(target=talk, args=(conn, client_addr))
        p.start()
```

```python
# 客户端
from socket import *


def client(ip, port):
    client_side = socket(AF_INET, SOCK_STREAM)
    addr = ip, port
    client_side.connect(addr)
    while True:
        msg = input('>>>').strip()
        if not msg: continue
        if not msg.isalpha(): continue
        client_side.send(msg.encode('utf-8'))
        data = client_side.recv(1024)
        print(data.decode('utf-8'))


client('127.0.0.1', 8080)
```

- 高大上版

```python
# server
from socket import  *
from multiprocessing import Process, Pool


def talk(conn):
    with conn:
        while True:
            try:
                msg = conn.recv(1024)
                if not msg: break
                conn.send(msg.upper())
            except Exception as e:
                print(e)
                break

def task(ip, port):
    with socket(AF_INET, SOCK_STREAM) as s:
        addr = ('127.0.0.1', 8080)
        s.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
        s.bind(addr)
        s.listen(5)
        conn, client_addr = s.accept()
        return conn, client_addr
if __name__ == '__main__':
    while True:
        conn, client_addr = task('127.0.0.1', 8080)
        p = Process(target=talk, args=(conn, ))
        p.start()

```

```python
# client
from socket import *

def task(ip, port):
    with socket(AF_INET, SOCK_STREAM) as c:
        addr = (ip, port)
        c.connect(addr)
        while True:
            msg = input('>>>').strip()
            if not msg: continue
            c.send(msg.encode('utf-8'))
            data = c.recv(1024)
            print(data.decode('utf-8'))

task('127.0.0.1', 8080)
```




### threading.Thread多线程实现服务端客户端简单socket通信

```python
# 服务端
from threading import Thread, get_ident
from socket import *


def server(ip, port):
    server_side = socket(AF_INET, SOCK_STREAM)
    server_side.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
    server_addr = ip, port
    server_side.bind(server_addr)
    server_side.listen(5)
    conn, client_addr = server_side.accept()
    return conn, client_addr


def talk(conn, client_addr):
    while True:
        try:
            msg = conn.recv(1024)
            if not msg:break
            print('receved from %s'%client_addr[0])
            if not msg:continue
            conn.send(msg.upper())
        except Exception as e:
            print(e)
            break

if __name__ == '__main__':
    while True:
        conn, client_addr = server('127.0.0.1', 8080)
        t = Thread(target=talk, args=(conn, client_addr))
        t.start()
        # t.join()   # 会导致只能同时一个客户端在线

```

```python
# 客户端
from socket import *


def client(ip, port):
    server_addr = ip, port
    client_side = socket(AF_INET, SOCK_STREAM)
    client_side.connect(server_addr)
    while True:
        msg = input('>>>').strip()
        if msg == 'quit':break
        if not msg: continue
        if not msg.isalpha(): continue
        client_side.send(msg.encode('utf-8'))
        data = client_side.recv(1024)
        print(data.decode('utf-8'))
    client_side.close()

client('127.0.0.1', 8080)

```


## 模拟抢票
### 使用Process实现

```python
import os
import json
from multiprocessing import Process, Lock

def check_tickets():
    with open('tickets', 'r') as rf:
        tickets = json.load(rf)
        print('剩余票数:%s'%tickets['count'])

def get_tickets():
    with open('tickets', 'r') as rf:
        tickets = json.load(rf)
        if tickets['count'] > 0:
            tickets['count'] -= 1
            json.dump(tickets, open('tickets', 'w'))
            print('%s抢票成功'%os.getpid())

def task(mutex):
    check_tickets()
    mutex.acquire()
    get_tickets()
    mutex.release()


if __name__ == '__main__':
    mutex = Lock()
    for i in range(50):
        p = Process(target=task, args=(mutex,))
        p.start()

```


## 生产者消费者模型
### 使用多进程multiprocessing.Process和队列multiprocessing.Queue实现
- 一个生产者对应多个消费者(1)

```python
from multiprocessing import Process, Queue, Manager
import time
import random
import os



def producer(q):
    for i in range(10):
        time.sleep(random.random())
        q.put(i)
        print('producer:%s 生产%s'%(os.getpid(), i))


def customer(q):
    while True:
        time.sleep(2)
        res = q.get()
        print('customer:%s 消费%s'%(os.getpid(), res))

if __name__ == '__main__':
    m = Manager()
    q = m.Queue(5)
    p = Process(target=producer, args=(q,))
    p.start()
    c_l = []
    for i in range(3):
        c = Process(target=customer, args=(q,))
        c_l.append(c)
        c.start()
    p.join()
    for c in c_l:
        c.join()
    print('这是主程序')

```

- 一个生产者对应多个消费者(2)

```python
from multiprocessing import Process, Queue
import time
import random
import os



def producer(q):
    for i in range(10):
        time.sleep(random.random())
        q.put(i)
        print('producer:%s 生产%s'%(os.getpid(), i))


def customer(q):
    while True:
        time.sleep(2)
        res = q.get()
        print('customer:%s 消费%s'%(os.getpid(), res))

if __name__ == '__main__':
    q = Queue(5)
    p = Process(target=producer, args=(q,))
    p.start()
    c_l = []
    for i in range(3):
        c = Process(target=customer, args=(q,))
        c_l.append(c)
        c.start()
    p.join()
    for c in c_l:
        c.join()
    print('这是主程序')
```


- 一个生产者对应多个消费者(3)

```python

from multiprocessing import Process, Queue
import time
import random
import os

q = Queue(5)
def producer(q):          # 这样传入的q保证了都是全局变量中同一个q
    for i in range(10):
        time.sleep(random.random())
        q.put(i)
        print('producer:%s 生产%s'%(os.getpid(), i))


def customer(q):          # 这样传入的q保证了都是全局变量中同一个q
    while True:
        time.sleep(2)
        res = q.get()
        print('customer:%s 消费%s'%(os.getpid(), res))

if __name__ == '__main__':
    p = Process(target=producer, args=(q,))
    p.start()
    c_l = []
    for i in range(3):
        c = Process(target=customer, args=(q,))
        c_l.append(c)
        c.start()
    p.join()
    for c in c_l:
        c.join()
    print('这是主程序')
```

- 上面的程序消费者消费完生产者生产的数据之后程序不会结束

```python
from multiprocessing import Process, JoinableQueue
import time
import random
import os


def producer(q):
    for i in range(10):
        time.sleep(random.random())
        q.put(i)
        print('producer:%s 生产%s'%(os.getpid(), i))
    q.join()


def customer(q):
    while True:
        time.sleep(2)
        res = q.get()
        print('customer:%s 消费%s'%(os.getpid(), res))
        q.task_done()

if __name__ == '__main__':
    p_l = []
    q = JoinableQueue(5)
    for i in range(3):
        p = Process(target=producer, args=(q, ))
        p_l.append(p)
        p.start()
    for j in range(3):
        c = Process(target=customer, args=(q, ))
        c.daemon = True
        c.start()
    for p in p_l:
        p.join()
    print('这是主程序')

``` 


> 创建进程的时候，会拷贝父进程的内存空间，**拷贝的不是引用，而是创建新对象**
> 如果子进程的任务函数的参数列表中包含了对父进程中变量的引用，那么同时会获得父进程内存空间中对应的变量的引用，看[bug](http://www.ziawang.com/python/python_bugs/producer_customer.html)




## 爬虫
### 进程池实现爬虫
```
    urls = [
        'https://www.baidu.com',
        'http://www.openstack.org',
        'https://www.python.org',
        'https://help.github.com/',
        'http://www.sina.com.cn/'
	'http://www.ziawang.com'
    ]
```

```python
from multiprocessing import Pool
import requests
import time

urls = [
    'https://www.baidu.com',
    'http://www.openstack.org',
    'https://www.python.org',
    'https://help.github.com/',
    'http://www.sina.com.cn/',
    'http://www.ziawang.com'
]

def get_page(url):
    response = requests.get(url)
    if response.status_code == requests.codes.ok:
        print('%s'%url, '爬取成功')
        return {'url':url, 'text':response.text}

def parser(response_dic):
    print('开始解析:%s'%response_dic)
    with open('parser_file', 'a') as af:
        af.write('%s : %s \n'%(response_dic['url'], len(response_dic['text'])))
        print('%s'%response_dic['url'], '解析成功')


if __name__ == '__main__':
    p = Pool(3)
    for url in urls:
        p.apply_async(get_page, args=(url, ), callback=parser)
    p.close()
    p.join()
    print('这是主进程')
```

- 回调时，是将get_page的结果传递给parser，而不是apply_async()的结果，因为apply_async()返回的是apply_result对象

```python

from multiprocessing import Pool
import requests
import time

urls = [
    'https://www.baidu.com',
    'http://www.openstack.org',
    'https://www.python.org',
    'https://help.github.com/',
    'http://www.sina.com.cn/',
    'http://www.ziawang.com'
]

def get_page(url):
    response = requests.get(url)
    if response.status_code == requests.codes.ok:
        print('%s'%url, '爬取成功')
        return {'url':url, 'text':response.text}

def parser(response_dic):
    print('开始解析:%s'%response_dic)
    with open('parser_file', 'a') as af:
        af.write('%s : %s \n'%(response_dic['url'], len(response_dic['text'])))
        print('%s'%response_dic['url'], '解析成功')


if __name__ == '__main__':
    p = Pool(3)
    obj_l= []
    for url in urls:
        p.apply_async(get_page, args=(url, ))
        obj_l.append(p)
    p.close()
    p.join()
    for obj in obj_l:
        print(obj)
    print('这是主进程')
```

- 使用concurrent.futures的ProcessPoolExecutor实现

```python
from concurrent.futures import ProcessPoolExecutor
import requests

urls = [
    'https://www.baidu.com',
    'http://www.openstack.org',
    'https://www.python.org',
    'https://help.github.com/',
    'http://www.sina.com.cn/',
    'http://www.ziawang.com'
]

def get_page(url):
    response = requests.get(url)
    if response.status_code == requests.codes.ok:
        print('%s'%url, '爬取成功')
        return {'url':url, 'text':response.text}
    else:
        print('%s 爬取失败'%url)

def parser(future_obj):
    response_dic = future_obj.result()
    print('开始解析:%s'%response_dic)
    with open('parser_file', 'a') as af:
        af.write('%s : %s \n'%(response_dic['url'], len(response_dic['text'])))
        print('%s'%response_dic['url'], '解析成功')

if __name__ == '__main__':
    p = ProcessPoolExecutor(3)
    for url in urls:
        res = p.submit(get_page, url).add_done_callback(parser)
    p.shutdown()
    print('主进程')
```



> p.submit()返回的是future对象，只有futures对象才能调用对应的add_done_callback()方法