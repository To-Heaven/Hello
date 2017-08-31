### Bugs——多进程并发实现服务端和客户端socket简单通信

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