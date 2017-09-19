### Bugs——多进程并发实现服务端和客户端socket简单通信
<hr>

```python
# 服务端
from socket import *
from multiprocessing import Process


def talk(conn, client_addr):
    while True:
        try:
            print('..........')
            msg = conn.recv(1024)
            print('received from  %s' % client_addr)
            if not msg: break
            conn.send(msg.upper())
        except Exception:
            break
    conn.close()


def server(ip, port):
    server_side = socket(AF_INET, SOCK_STREAM)
    server_side.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
    server_side_addr = ip, port
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
    client _side= socket(AF_INET, SOCK_STREAM)
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

<hr>


```python
# 服务端
import select
from socket import *


address = '127.0.0.1', 8080

with socket(AF_INET, SOCK_STREAM) as s:
    s.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
    s.bind(address)
    s.listen(5)
    s.setblocking(False)
    read_list = [s,]
    while True:
        rl, _, _ = select.select(read_list, [], [])
        for fd in rl:
            if fd == s:
                conn, client_addr = fd.accept()
                rl.append(conn)
            else:
                try:
                    msg = fd.recv(1024)
                    if not msg:
                        fd.close()
                        rl.remove(fd)
                        continue
                    fd.send(msg.upper())
                except BlockingIOError:
                    pass
                except ConnectionResetError:
                    fd.close()
                    rl.remove(fd)
```

```python
# 客户端
from socket import  *

server_addr = '127.0.0.1', 8080
with create_connection(server_addr) as c:
    while True:
        msg = input('>>>').strip()
        if not msg:continue
        c.send(msg.encode('utf-8'))
        data = c.recv(1024)
        print(data.decode('utf-8'))
```

<hr>

```python
# 服务端
from socket import *
import select

server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
server.bind(('127.0.0.1', 8080))
server.listen(5)
server.setblocking(False)

read_list = [server, ]

while True:
    rl, _, _ = select.select(read_list, [], [])
    for file_obj in rl:
        if file_obj == server:
            conn, _ = file_obj.accept()
            print(conn)
            rl.append(conn)
        else:
            try:
                msg = file_obj.recv(1024)
                if not msg:
                    file_obj.close()
                    rl.remove(file_obj)
                    continue
                file_obj.send(msg.upper())
            except ConnectionResetError:
                file_obj.close()
                rl.remove(file_obj)
```

```python
# 客户端
from socket import *

with create_connection(('127.0.0.1', 8080)) as c:
    while True:
        msg = input('>>>').strip()
        if not msg:continue
        c.send(msg.encode('utf-8'))
        data = c.recv(1024)
        print(data.decode('utf-8'))
```



### Bugs——生产者消费者模型
> [解决不了看这里](https://stackoverflow.com/questions/43155553/python-3-5-multiprocessing-pool-and-queue-dont-work)

```python
import multiprocessing
import time

class Test:
    def __init__(self):
        self.pool = multiprocessing.Pool(1)
        #self.queue = multiprocessing.Queue()

    def subprocess(self):
        for i in range(10):
            print("Running")
            time.sleep(1)
        print("Subprocess Completed")

    def start(self):
        self.pool.apply_async(func=self.subprocess)
        print("Subprocess has been started")
        self.pool.close()
        self.pool.join()

    def __getstate__(self):
        self_dict = self.__dict__.copy()
        del self_dict['pool']
        return self_dict

    def __setstate__(self, state):
        self.__dict__.update(state)

if __name__ == '__main__':
    test = Test()
    test.start()
```


## bugs concurrent.futures实现爬虫

```python

import requests
from concurrent.futures import ProcessPoolExecutor


def get_page(url):
    print(url)
    response = requests.get(url)
    if response.status_code == requests.codes.ok:
        print(response.url)
        return response



def parser(future_obj):
    response = future_obj.result()
    filename = str(hash(response.url))
    with open(filename, 'wb') as fw:
        for chunk in response.iter_content(1024):
            fw.write(chunk)
        else:
            print(response.url, '完毕')


urls = [
    'https://www.baidu.com',
    'http://www.openstack.org',
    'https://www.python.org',
    'https://help.github.com/',
    'http://www.sina.com.cn/',
    'http://www.ziawang.com'
]



if __name__ == '__main__':
    p = ProcessPoolExecutor(2)
    for url in urls:
        res = p.submit(get_page, (url, )).add_done_callback(parser)
    p.shutdown()
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

    def __init__(self, url):
        self.url = url
        self.response = requests.get(url)
        print(self.url, self.response.status_code)
        if self.response != requests.codes.ok:
            raise ConnectionError('连接失败')
        print('1111')
        self.parser()

    def parser(self):
        print('%s begin parser'%self.response.url)
        filename = str(hash(self.url))
        with open(filename, 'w') as fw:
            for item in self.response.headers.items():
                fw.write('{} : {} \n'.format(*item))
            for chunk in self.response.iter_content(self.chunksize,
                                                    decode_unicode='utf-8'):
                fw.write(chunk)
            else:
                print('%s complete'%self.url)

```
