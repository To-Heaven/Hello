# 进程、线程、协程
## socket通讯
### 多进程并发实现服务端和客户端socket简单通信

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

### 多线程实现服务端客户端简单socket通信

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