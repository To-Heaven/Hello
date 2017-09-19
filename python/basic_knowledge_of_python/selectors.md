# selectors 模块

## 什么是file object（文件对象）
- file object 是一个抽象的概念，不能像字面上意思认为只是一个简单的文件对象！
	- file object 也是 file-like对象，streams流
- 在python中，一个文件是不是file object取决于该对象的创建方式
	- 可以是磁盘上实实在在存在的文件
		- 普通文本文件
		- 二进制文件
	- 也可以是内存中其他类型的对象或者用于进行通讯(communication)的对象，比如
		-  标准输入输出    `sys.stdout, sys.stdin,  sys.stderr`等
		-  缓存
		-  socket套接字 
		-  管道
		-  队列
		-  等等等


## DefaultSelector
- 操作系统监测select提供的file obejct，而每种操作系统支持的file object是不一样的
	- 对于Windows操作系统，只支持socket套接字对象，不支持管道pipe等其他对象
	- 对于Linux操作系统，基本上所有对象都支持，比如socket，pipe，queue，file，binary-file等
- 对于不同的操作系统，其能够支持的select模块的轮询对象也是不同的
	- 比如epoll()方法返回的轮询对象就不被Windows操作系统支持，但是却可以被Linux系统支持

- 基于上述情况，DefaultSelector()会帮我们自动根据操作系统选择出一个最适合的轮询对象，并返回一个对象，该对象分别对应select模块中的类
	- selectors.SelectSelector  --->  select.select()
	- selectors.EpollSelector  --->  select.epoll()
	- selectors.PollSelector  --->  select.poll()
	- 这三个类都继承自selectors.BaseSelector

## Events
- `EVENT_READ`——available for read
- `EVENT WRITE`——available for write 



## Methods of  BaseSelector (SelectSelector, EpollSelector,PollSelector)
> BaseSelector是一个抽象类，不能被实例化，其实使用DefaultSelector获取适合操作系统的其子类就好了。


- sel.register(fileobj, events, data=None)
	- 注册一个file object 对象用于选择，监视这个对象的I/O事件
	- 返回值是一个SelectorKey类的实例
	- raise ValueError in case of invalid event mask or file descriptor
	- raise KeyError  if the file object is already registered

- sel.unregister(fileobj)
	- 将file object 从被监视的对象组中移除
	- **注意，一个file object 在关闭操作之前应该先完成unregister()**
	- file obejct必须是已经注册过的对象，否则会爆出KeyError错误
	- 返回值为一个SelectKey实例。
	- raise ValueError if fileobj is invalid (e.g. it has no fileno() method or its fileno() method has an invalid return value).

- sel.modify(fileobj, events, data=None)
	- 修改fileobj监听地事件和指向地data值
	- Change a registered file object’s monitored events or attached data.

```
sel.mydify(fileobj, events, data=None)
# 等价于
sel.unregister(fileobj)
sel.register(fileobj, events, data=None)
```

- sel.select(timeout)
	- 阻塞程序直到所有注册过的file object全部处于就绪态，或者触发timeout
	- 返回值是一个元组构成地列表`[(key, events), (key, events), (key, events)......]`
		- key是对应就绪态file object 的SelectKey
	- **在信号处理器(singal handler)没有触发异常的情况下，如果触发了timeout，sel.select()会重新尝试一次而不是返回一个空列表**，在3.5版本之前是返回一个空列表

- sel.close()
	- 关闭选择器selector

- sel.get_key(fileobj)
	- 返回fileobj对应的SelectorKey
	- raises KeyError if the file object is not registered.

- sel.get_map()
	- 返回所有file object 及其对应的SelectorKey


## SelectorKey  Object
- 一个namedtuple，存放了一个file object的信息
	- fileobj
	- fd    底层文件描述符
	- events   fileobj 等待的事件
	- data    Optional  data associated to this file object


## 使用selectors实现简单的socket套接字通信

```python
# 服务端
from socket import *
import selectors

sel = selectors.DefaultSelector()


def accept(fileobj, mask):
    conn, _ = fileobj.accept()
    sel.register(conn, selectors.EVENT_READ, read)


def read(conn, mask):
    try:
        data = conn.recv(1024)
        if not data:
            print('closing', conn)
            sel.unregister(conn)
            conn.close()
            return  # 代替之前链接循环中的break
        conn.send(data.upper())
    except Exception as e:
        print(e)
        print('closing', conn)
        sel.unregister(conn)
        conn.close()


server = socket(AF_INET, SOCK_STREAM)
server.bind(('127.0.0.1', 8080))
server.listen(5)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
server.setblocking(False)
sel.register(server, selectors.EVENT_READ, accept)

while True:
    events = sel.select()
    for key, mask in events:
        callback = key.data
        callback(key.fileobj, mask)
```

```python
# 客户端
from socket import *

with create_connection(('127.0.0.1', 8080)) as c:
    while True:
        msg = input('>>>').strip()
        if not msg: continue
        c.send(msg.encode('utf-8'))
        data = c.recv(1024)
        print(data.decode('utf-8'))
```
