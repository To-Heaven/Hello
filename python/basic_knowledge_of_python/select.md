# select 模块

## 注意
- 在大部分操作系统上都能调用select模块提供的`select()`和`poll()`方法，但是以下方法要分操作系统（只介绍常用操作系统）
	- `epoll()`   Linux操作系统
	- `kqueue()` BSD 伯克利套件
	- `devpoll` solaris操作系统
- 在Windows操作系统，`select()`和`poll()` 只能用于socket套接字相关操作，在其他操作系统上，它们可以用于file-types类型的操作，比如在Unix操作系统上，可以对管道PIPE进行操作，但是不能用于确定文件自上次read操作之后的变化

## select.epoll(sizehint=-1, flag=0)
> 只支持Linux操作系统    
> 该函数参数请忽略


- 返回一个轮询对象，用作I/O事件的触发接口
- 注意：
	- epoll对象支持上下文管理，即可以使用`with`语句

### Attribute of Trigger Epoll Obejcts
> level epoll  水平触发，也叫条件触发，备件空的file obejct 处于就绪态时，epoll.poll()会通知处理程序对该file object读写，如果此次通知后处理程序没有将数据一次性读写完（读写缓冲区小于读写数据大小），接着下一次调用epoll.poll()，epoll.poll()会继续通知处理程序在上次没读完数据的file object，直到数据写完（稳定可靠，但效率低）     


> trigger epoll 即边缘触发，只告诉进程那些file object处于就绪状态，并且只会通知进程一遍，即使程序没有采取任何行动，也不会再次通知，如果缓冲池中存在没读写完的数据，下次调用epoll.poll()不会通知该fileobject，直到该file object再次处于就绪态才会通知（效率高，但有时候不可靠）   


- epoll.closed
	- 返回True，如果epoll对象是关闭状态的


### Methods of Trigger Epoll Obejcts

- epoll.close()
	- 关闭一个epoll对象
- epoll.fileno()
	-  Return the file descriptor number of the control fd.
- epoll.fromfd(fd)
	- 返回一个epoll对象，通过fd文件描述符创建
- epoll.register(fd [,eventmask])
	- 为一个epoll对象注册一个文件描述符（类似于绑定）
- epoll.unregister(fd)
	- 从epoll对象上删除已注册的文件描述符
- epoll.modify(fd[, eventmask])
	- 修改注册了的文件描述符
- epoll.poll(timeout=-1, maxevents=-1)
	- 等待事件，timeout为等待时间
	- 当事件的信号被中断的时候，会重新计算超时时间，除非信号处理程序(singal handler)出现了异常
		- 在3.5版本之前，如果超时会引发`InterruptedError`


- epoll实现简单套接字通信

```python
# 服务端
import socket
import select
  
s = socket.socket()
s.bind(('127.0.0.1',8888))
s.listen(5)
epoll_obj = select.epoll()
epoll_obj.register(s,select.EPOLLIN)
connections = {}
while True:
 events = epoll_obj.poll()
 for fd, event in events:
  print(fd,event)
  if fd == s.fileno():
   conn, addr = s.accept()
   connections[conn.fileno()] = conn
   epoll_obj.register(conn,select.EPOLLIN)
   msg = conn.recv(200)
   conn.sendall('ok'.encode())
  else:
   try:
    fd_obj = connections[fd]
    msg = fd_obj.recv(200)
    fd_obj.sendall('ok'.encode())
   except BrokenPipeError:
    epoll_obj.unregister(fd)
    connections[fd].close()
    del connections[fd]
  
s.close()
epoll_obj.close()
```

```python
# 客户端
import socket
  
flag = 1
s = socket.socket()
s.connect(('127.0.0.1',8888))
while flag:
 input_msg = input('input>>>')
 if input_msg == '0':
  break
 s.sendall(input_msg.encode())
 msg = s.recv(1024)
 print(msg.decode())
  
s.close()
``







## select.select(rlist, wlist, xlist[,timeout])
- Unix操作系统中select()系统调用的接口
- `waitable objects` ---> 套接字或者文件描述符
	- python file-like objects  (`sys.stdin`)
	- objects returned by` open() `or `os.popen()`
	- socket obejct returned by `socket.socket()`
	- a wrapper class defined by yourself , as long as it has an fileno() method
- *parameters*
	- rlist：'waitable objects ' wait until ready for reading
	- wlist：'waitable objects ' wait until ready for writing
	- xlist：'waitable objects ' wait for an “exceptional condition”
	- timeout：
		- 未指定时：该方法会阻塞直到socket或文件描述符准备好
			- 关于'准备'一词，请参考[协程](http://www.ziawang.com/python/basic_knowledge_of_python/coroutines.html)中IO阻塞的解释（操作系统内核要经历两个阻塞阶段，即`wait for data `和 `copy data from kernel to user`，该准备阶段即`wait for data `阶段）
		- 指定时：阻塞指定时间
		- 指定为'0'：不阻塞
		- __当timeout触发时，如果没有socket或文件描述符对象准备好，返回列表中的三个子集均为空__


```python
from socket import *
import select

server = socket(AF_INET, SOCK_STREAM)
server.bind(('127.0.0.1', 8080))
server.listen(5)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)

with server:
    ready_l = [server, ]
    while True:
        rl, *_ = select.select(ready_l, [], [], 5)
        print(rl)		# 没有客户端请求链接时，每一次timeout触发，select就截除阻塞一次，在终端会每个5S打印出一个空列表

```


- 注意参数的格式
	- 在Unix操作系统中，是允许三个列表全是空列表的
	- 但是在Windows操作系统中，三个列表不能都为空列表
		- 由于Windows操作系统中，对select.select()的调用只适用于socket套接字对象，因此，在建立socket套接字时，应将存放服务端套接字的列表做为实际参数传入进select.select()的rlist参数位置
- 返回值是一个列表，列表中有三个子集分别存放着对应`rlist  wlist  xlist`中处于`read`状态的套接字或文件描述符



## 举例与注意
- select 模块实现socket套接字通信

```python
# 服务端
from socket import *
import select

server = socket(AF_INET, SOCK_STREAM)
server.bind(('127.0.0.1', 8080))
server.listen(5)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
read_list = [server, ]

while True:
    rl, *_ = select.select(read_list, [], [], 5)
    for ready_obj in rl:
        if ready_obj == server:
            conn, _ = ready_obj.accept()
            read_list.append(conn)
        else:
            try:
                msg = ready_obj.recv(1024)
                if not msg:
                    ready_obj.close()
                    read_list.remove(ready_obj)
                ready_obj.send(msg.upper())
            except ConnectionResetError:
                ready_obj.close()
                read_list.remove(ready_obj)
# -------------------------------------------------------------------
from socket import *
import select

server = socket(AF_INET, SOCK_STREAM)
server.bind(('127.0.0.1', 8080))
server.listen(5)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
# server.setblocking(False)

with server:
    ready_l = [server, ]
    while True:
        rl, *_ = select.select(ready_l, [], [], 60)
        print(rl)
        for file_obj in rl:
            if file_obj == server:
                conn, _ = server.accept()
                print('get ', conn.getsockname())
                ready_l.append(conn)
            else:
                try:
                    msg = file_obj.recv(1024)
                    if not msg:
                        file_obj.close()
                        ready_l.remove(file_obj)
                    file_obj.send(msg.upper())
                except ConnectionResetError:
                    file_obj.close()
                    ready_l.remove(file_obj)
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

- 分析：
	1. 程序调用select时，整个进程都会被block，block的同时，内核会监视所有select负责的socket，当其中的任何一个socket准备(waiting for data 阶段)好了，select.select()就会返回。
	2. 用户拿到完成`waiting for data `阶段的套接字之后，再调用read操作将data从内核拷贝到用户进程内存空间中

- 注意：
	1. select.select()返回是只完成了第一步`waiting for data`阶段的对象，后续的`copy data from kernel to user`需要用户自己来完成
	2. 上述整个进程是被select阻塞的，而不是socket套接字通讯时的阻塞

- select的优势
	- 可以同时处理多个connection
	- 占用资源小
		- select的事件驱动模型只用单线程（进程）执行


- select的缺点
	- 如果处理的connection的数据不是很多，使用了select/epoll的web服务器不见得比multiprocessing.thread + blocking IO 的web性能高。**select只适用于处理多个请求，不适用于单个连接或较少的链接**
	- 当需要检测的file object 数目较大的时候，select()需要维护大量的文件描述符的结构，select()接口本身需要消耗大量的事件去轮询各个file object 。解决这个问题可以使用epoll()接口去代替select()接口。因此对于select不是'事件驱动'模型的最佳选择
	- select()模型将事件监测与事件响应夹杂在一起，当事件响应的执行体非常庞大的时候，该模型带来的反而是灾难
	- 实际上，单个进程能够监视文件描述符的数量存在最大限制，Linux上是1024，不过可以通过修改宏定义甚至重新编译内核的方式提升这个限制

- epoll的优点
	- 同时支持水平触发和边缘触发
	- 节省了文件描述符在系统调用时复制的开销
		- epoll采用了内存映射(mmap)技术。对于就绪的file object ，当调用epoll_wait()得到就绪状态的文件描述符的时候，该方法返回的并不是实际的描述符对象，而是就绪态的描述符数量，因此可以根据程序指定的文件描述符数量来从数组中获取对应的描述符
	-  epoll采用了基于事件的就绪通知方式。
		-  在select/poll中，进程调用了指定的方法后，内核才能对所有监视的file object对象（文件描述符）进行描述。
		-  epoll事先通过epoll_ctl()注册一个文件描述符，一旦某个文件描述符就绪，内核就会采用回调的机制迅速激活该文件描述符，调用了epoll_wait()的进程就会被通知