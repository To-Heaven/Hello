# 协程
## 协程是什么鬼？
- 在一个线程内，（伪）并发的实现多个任务。



## 并发的本质
- 在多道技术中，CPU存在时间上的复用，即CPU要在多个任务之间快速地来回切换，在CPU切换回一个任务的时候，肯定是要接着这个任务上一次被CPU处理状态继续往下执行地，否则来回切换将没有任何意义。因此，CPU快速来回切换要满足地一个非常重要地前提就是保存任务地运行状态。

- 本质
	1. CPU快速在任务之间快速来回切换
	2. 每个任务被执行地状态要保存起来


- CPU中线程（任务）的三种状态
	1. 运行
		- 顾名思义，就是该任务正在被CPU执行地状态
	2. 阻塞
		- 一个任务，如果任务的内容中存在了I/O操作，CPU在切换到这个任务的时候，发现这个任务处于阻塞状态，CPU就不会执行这个任务，直到CPU切换到这个任务的时候发现这个任务不阻塞了，即I/O结束了，CPU才会开始执行这个任务。
	3. 就绪
		- 接下来任务不是I/O任务，而是运算型任务，那么这个任务就会在CPU不断来回切换的过程中被执行，任务的这中状态就是就绪态。
		- 就绪态的任务随时准备好被运行，但是不一定立即运行
		- 就绪态的任务可以直接被CPU调用执行


- yield关键字
	- yield关键字在生成器中可以保存函数运行到该yield位置时的状态，当生成器再次被next调用的时候，生成器函数将继续从该位置继续往下运行，直到遇到了下一个yield。
	- __send__可以把一个函数的结果传递给另外一个函数，这样就可以实现单线程内程序之间的切换

```python
# yield 并发生产者消费者
import time

def customer():
    while True:
        res = yield '接收procuder发送的数据'

def producer():
    c = customer()
    next(c)
    for i in range(10000000):
        c.send(i)

start = time.time()
producer()
end = time.time()
print('total time: %s'%(end-start))
------------------------------------------------------------------
total time: 2.500823736190796

Process finished with exit code 0
```

- 上面的例子中，遇到的是计算密集型的并发，并没有遇到I/O阻塞，因此与产生相同结果的串行相比反而会浪费cpu性能，降低效率。因为在不存在I/O的任务中，单纯的切换是浪费时间的，切换的过程反而降低运行效率

> 多进程（多进程）中，线程（进程）之间的切换由操作系统调度的，这种切换属于操作系统级别的切换，操作系统中运行着除了这个多线程（多进程）之外的其他线程和进程，因此对于一个线程（进程）而言，系统级别的不断切换执行该任务的效率要低。用户级别控制的切换要比操作系统控制切换要快得多。
> 同一个线程下多个密集计算型任务之间单纯的切换没有意义，必须等到有IO才能有意义

```python
import time

def customer():
    pass

def producer():
    l = []
    for i in range(10000000):
        l.append(i)

start = time.time()
producer()
end = time.time()
print('total time : %s'%(end-start))
--------------------------------------------------------
total time : 2.821392059326172			# 理论上，他确实要比上面例子要小，大概我的电脑是假的

Process finished with exit code 0
```

- 综上所述，要想让一个线程最大化的发挥其效率，必须满足以下两点
	1. **前提：** cpu可以在该线程下的多个任务之间快速来回切换，并将任务切换之前的状态保存下来
	2. **关键：**要检测该线程下的任务是否存在I/O阻塞。任务一旦运行到了阻塞部分，cpu就应该切换到该线程下其他任务并执行

- 在回头看上述yield的代码，如果在任务中出现了I/O阻塞，并不能实现切换任务，而是会等待阻塞完成之后继续执行该任务

```python
import time

def customer():
    while True:
        res = yield '接收procuder发送的数据'

def producer():
    c = customer()
    next(c)
    for i in range(10000000):
        time.sleep(0.00000001)		# yield无法实现I/O切换
        c.send(i)

start = time.time()
producer()
end = time.time()
print('total time: %s'%(end-start))

```

- greenlet模块
- [点击查看greenlet模块内容]()
- 简单来说，greenlet模块简化了上述yield实现多个任务的切换，因为使用yield 的时候。生成器函数每一都要先激活，然后才能利用send去想yield传递值，当线程中的任务非常多的时候，显然不能这样做。
- **但是，greenlet模块仍然和yield一样存在缺点——无法检测和处理任务中的I/O阻塞**








> 线程和进程是内核级别的，由操作系统来进行控制调度，如果一个线程或进程出现I/O或者执行时间过长，就会被迫交出cpu的执行权限切换到其他线程或进程去执行

## 协程的本质和特点
- **本质**：在单线程下，由用户自己控制一个任务一旦遇到了I/O阻塞就切换到另外一个任务去执行，以此来提升效率
- **特点**
	1. 协程的开销比线程更小，属于程序级别的切换，当协程中的任务出现了I/O阻塞的时候，就会切换到其他任务上去执行，这样就可以在应用程序级别将I/O最大限度的压缩，从而魔火操作系统，防止在内核级别上因为程序I/O导致频繁的切换降低效率。
	2. 修改共享数据不需要加锁
	3. 一旦协程出现阻塞，整个线程都会阻塞
	4. 用户可以在程序中保存多个控制流的上下文线

> 在协程中，如何检测并处理I/O阻塞呢？



gevent
用户软件级别的来回切换，操作系统感知不到，效率更高，因为操作系统管理的线程多

dummyThread
一个线程下多个任务来回切换的时候，每一个任务都对应一个dummyThread

知识将进程和线程控制在操作系统能够接受的范围内，如果客户端非常多，远大于进程池数目，还是不能解决存在IO的进程和线程

tcp挥手照收 	
socket


## IO模型
- 调用方式
	- 同步调用
		- 定义：当发出一个功能调用时，在没有得到结果之前，该调用的结果不会返回，需要等待最终要返回的结果.
		- 绝大多数的函数，multiprocessing.Pool中的apply对进程任务的调用就是一个同步调用，该方法会一直等待任务完成并返回一个结果，不管该任务是否存在I/O阻塞，都会原地等在该任务完成
		- concurrent.futures.ThreadPoolExecutor.submit().result()
		- concurrent.futures.ProcessPoolExecutor.submit().result()
	- 异步调用
		- 定义：当发出一个功能调用时，调用者不会立即得到调用的结果，当该异步功能完成之后，通过状态、通知或回调来通知调用者。
			1. **通过状态**：每隔一定时间就需要见车一次，效率低（循环检查）
			2. **通过通知**：效率高，不需要额外的操作
			3. **通过回调**：multiprocessing.Pool 模块的apply_async()方法提供了回调功能，会自动的将异步调用产生的结果传递给另一个callable对象
		- concurrent.futures.ProcessPoolExecutor().submit()
		- concurrent.futures.ProcessPoolExecutor().submit() 
- 阻塞和非阻塞
	- 阻塞
		- 定义：调用结果返回之前或任务完成之前，当前任务被挂起，最常见导致阻塞的场景是I/O阻塞。
		- 与同步调用不同
			1. 同步调用的任务的状态是就绪态，调用者会一直等待任务结束或结果返回
			2. 当任务或调用遇到I/O阻塞的时候，该任务或调用就会被挂起，处于阻塞态，直到阻塞结束，该任务才会从阻塞态转换成就绪态，才有可能在接下来被cpu调用
	- 非阻塞：在不能立刻得到结果之前也会立刻返回



## Network IO  中的两个阶段（网络IO）
- **以socket网络编程中服务端和客户端交互为例**，服务端accept客户端并建立连接的过程就包含了IO的两个阶段
	- wait for data（准备） 阶段。脚本中程序执行server.accept()是应用软件级别的命令，python解释器会将这个命令请求发送给操作系统（即用户进程调用recvfrom系统调用），操作系统控制硬件（网卡）来监听客户端client.connect()发送来的数据（比如一个tcp包）。
		1. 这个过程是阻塞的，操作系统会一直处于监听状态，直到收到了客户端发送过来的数据
		2.  在服务端操作系统等待阶段，服务端进程的的整个进程被阻塞，当kernel等第一步完成后，她就会将数据从kernel中拷贝到用户内存，也就是下一个阶段
	- copy data from kernel to user阶段。将数据从内核态的内存拷贝到用户态内存中
		1. 这个过程显然也是存在I/O阻塞的
		2. 数据从内核态的内存被copy到了用户态的内存中，然后kernel会返回一个结果，用户的进程截除block状态。

##  Blocking IO  ——阻塞IO   
![Blocking IO  ——阻塞IO](https://github.com/ZiaWang/Hello/blob/master/picture/blockingio.png?raw=true)

- 准备阶段和数据拷贝阶段都被阻塞了
	- 在socket套接字网络通讯中，accept()，recv()等都在这两个阶段产生了阻塞
		1. sever.accept()，服务端等待客户端连接，如果客户端没有发起连接请求或因为其他原因连接失败，那么服务端就会一直阻塞在server.accept()这个位置，直到由客户端连接
		2. server.recv()  client.recv()  接收端等待发送端发送来的数据，如果发送端没有发送数据后者发送失败，那么接收端就会一直阻塞在recv()这个位置

```python
# 服务端

server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
addr = ('127.0.0.1', 8080)
server.bind(addr)
server.listen(5)


while True:
    conn, addr = server.accept()		# Blocking IO
    while True:
        try:
            msg = conn.recv(1024)		# Blocking IO
            print(addr, 'connected')
            if not msg:break
            conn.send(msg.upper())
        except Exception:
            break
```


```python
# 客户端
from socket import *

client = socket(AF_INET, SOCK_STREAM)
server_addr = ('127.0.0.1', 8080)
client.connect(server_addr)

while True:
    msg = input('>>>').strip()        # IO
    if not msg: continue
    client.send(msg.encode('utf-8'))
    data = client.recv(1024)		# Blocking IO
    print(data.decode('utf-8'))
```

##  Nonblocking IO —— 非阻塞IO
![Blocking IO  ——阻塞IO](https://github.com/ZiaWang/Hello/blob/master/picture/nonblockingio.png?raw=true)

- 在非阻塞IO中
	1. 如果服务端发起了server.accept()请求的时候，如果kernel中的数据还没有准备好，那么accept并不会阻塞服务器的进程，而是会抛出一个BlockingIOError
	2. 对于socket套接字来说，报错可以使用try...except...分支来处理，将这部分代码放在无限循环语句中就可以不断对客户端发送的请求进行接收，并且对于已经创建的链接，保存在一个列表中，这样可以在这段时间间隔中完成客户端和服务端之间的通讯。
	3. 在整个套接字建立链接并进行通讯过程中，accept建立连接和轮询过程中的通讯是串行的，简单说就是客户端和服务端建立连接必须等所有存在于链接列表中已经建立好的客户端与服务端之间的通讯过程结束之后才能建立新的链接。
		- 显然，在客户端和服务端进行通讯的过程中，新的客户端完全有可能会像服务端发送connect链接请求的，但是这个时候服务端在和已建立的客户端们进行交互。所以非阻塞IO也是有缺点的
- **注意**
	1. 轮询：非阻塞的recvform系统调用调用之后，进程并没有被阻塞，内核马上返回给进程，如果数据还没准备好，此时会返回一个error。进程在返回之后，可以干点别的事情，然后再发起recvform系统调用。重复上面的过程，循环往复的进行recvform系统调用。
	2. 轮询检查内核数据，如果内核数据准备好了，再拷贝该数据到进程，进行其他的数据处理。**这个把数据从内核态内存拷贝到用户态内存的过程仍然是存在IO阻塞的**
	3. **非阻塞式IO中，用户进程其实是需要不断的主动询问kernel数据准备好了没有。**

- 综上所述
	1. 循环调用recv()将大幅度推高CPU占用率，可能会出现卡顿
	2. 任务完成的响应延迟增大了，因为每过一段时间才去轮询一次read操作，而任务可能在两次轮询之间的任意时间完成。这会导致整体数据吞吐量的降低。

> 什么是吞吐量throughput？
> 吞吐量是指对网络、设备、端口、虚电路或其他设施，单位时间内成功地传送数据的数量（以比特、字节、分组等测量）


```python
# 服务端
from socket import *


server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
addr = ('127.0.0.1', 8080)
server.bind(addr)
server.listen(5)
server.setblocking(False)

conn_l = []
while True:
    try:
        print(conn_l)
        conn, client_addr = server.accept()
        conn_l.append(conn)
    except BlockingIOError:
        for conn in conn_l[:]:		# 遍历过程中不要对遍历对象做更改，如果要更改可以对其副本进行遍历
            try:
                msg = conn.recv(1024)
                conn.send(msg.upper())
            except ConnectionResetError:
                conn.close()
                conn_l.remove(conn)

# conn_l = []
# del_l = []			# 这种方法，代码太冗余
# while True:
#     try:
#         print(conn_l)
#         conn, addr = server.accept()
#         conn_l.append(conn)
#
#     except BlockingIOError:
#         for conn in conn_l:
#             try:
#                 msg = conn.recv(1024)
#                 if not msg:continue
#                 conn.send(msg.upper())
#             except BlockingIOError:
#                 pass
#             except ConnectionResetError:
#                 del_l.append(conn)
#         for i in del_l:			
#             i.close()
#             conn_l.remove(i)
#         pass
```


```python
# 客户端
from socket import *


client = socket(AF_INET, SOCK_STREAM)
server_addr = ('127.0.0.1', 8080)
client.connect(server_addr)

while True:
    msg = input('>>>').strip()
    if not msg: continue
    client.send(msg.encode('utf-8'))
    data = client.recv(1024)
    print(data.decode('utf-8'))
```

##  IO multiplexing  IO多路复用
![IO multiplexing 多路复用](https://github.com/ZiaWang/Hello/blob/master/picture/iomultiplexing.png?raw=true)

- [select模块]()
- 原理：
	- 不断轮询所负责的socket（包括套接字，建立的conn链接），当某个socket有数据到达了，就会通知用户进程，但是对数据的处理需要用户自己来完成（比如套接字建立新链接，发送端发送数据等）

- 过程：
	1. 用户进程调用select方法，整个进程被block，这是因为内核kernel在监听所有存在于select参数的列表中的socket，只要其中任何一个数据第一个阶段即wait for data 转备好了，select方法就会返回该套接字。
	2. 用户在进程中再调用read操作（recv，send等），将数据从kernel拷贝到用户进程。


##  asynchronous IO



