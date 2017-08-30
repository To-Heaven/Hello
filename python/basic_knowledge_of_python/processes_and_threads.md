## 操作系统的两大功能
1. 为应用软件提供各种功能的抽象接口
	- 隐藏了丑陋复杂的各种硬件接口，为应用程序提供了良好的接口
2. 将多个应用程序对硬件资源的竞态访问变得有序化
	- 进程之间的管理和协调调度是由操作系统完成的

> __进程的调度，分配给哪一个CPU运行（多核），是操作系统说了算__

##  多道技术——针对单核，实现（伪）并发
- 空间上的复用
	- 内存中可以同时存在多个程序
		- Note：各个程序内存空间之间必须实现物理级别的隔离
- 时间上的复用
	- CPU可以非常快的在各个程序之间来回的切换
		- CPU切换的两种条件
			1. 当一个程序占用CPU时间过长的时候
			2. 当CPU遇到了IO阻塞的时候
				- 比如在主进程中，将子进程加载到内存的时候会产生IO延迟，此时CPU不会一直等待到子进程加载内存完毕，而是会去先执行其他进程

## 什么是进程
- 软件运行过程的一个抽象的概念（程序运行过程的抽象）
- 进程用来把一个程序的一个运行过程中所需要的资源集中整合在一起，是一个资源单位。
- 进程与程序
	- 进程是一个程序运行过程的抽象
	- 而程序就是一个文件中的一堆代码
		- 这个程序每运行一次就会创建一个新的进程

- 以下两种情况导致一个进程再逻辑上不能进行
	1. 进程挂起(进程自身原因)
		- 进程中存在IO阻塞，该进程占用的CPU就会让出给其他进程去执行，保证CPU一直在工作
	2. 操作系统层面(与进程无关)
		-  一个进程占用时间过多或者优先级等原因，造作系统会调用其他进程去使用CPU

- 进程的三种状态
	- 运行
	- 阻塞
	- 就绪



## 并发与并行
- 并行是针对多核CPU而言的，对于具有多个CPU的一台计算机来说，他可以同时并行运行多个进程
	- 该进程数 = CPU个数

- 并发再单核CPU和多核CPU上都会出现。因为即使是多核CPU，其数量也无法满足计算机中程序对进程数的需求
	- 并发就是依靠多道技术来实现伪并行效果

-  **实现并发的核心**
	- 硬件每中断一个进行着的进程，操作系统都要把该进程运行中的所有状态记录下来
	- 操作系统有一个进程表，每一个进程占用的进程表项都会被记录进去
		- 该表存放了进程状态的重要信息：程序计数器、堆栈指针、内存分配状况、所有打开文件的状态、帐号和调度信息，以及其他在进程由运行态转为就绪态或阻塞态时，必须保存的信息，从而保证该进程在再次启动时，就像从未被中断过一样。

## 创建子进程
- 创建子进程的四种形式
	- 系统初始化
	- 一个进程在运行中创建子进程
	- 用户与操作系统之间的交互式请求（打开软件）
	- 一个批处理作业的初始化

- 所有创建新进程的方式都是基于操作系统中创建新进程的系统调用而创建的。
	1. Linux中，通过系统调用fork来创建子进程。创建的子进程在初始化的时候会复制父进程的内存空间，两者有相同的存储映像、同样打开的文件。
		- 在shell中，每执行一个命令就会创建一个子进程
	2. Windows中系统调用是CreateProcess，负责把正确的程序装入新进程

- 但是在不同的操作系统中，关于内存的分配是不一样的
	- 相同点：
		- 创建完进程之后，父进程和子进程有各自不同的地址空间（多道技术要求在物理层面实现进程之间内存的隔离），任何一个进程再其地址空间中的修改都不会影响到另一个进程
	- 不同点：
		- 在Linux系统中，子进程继承自父进程，会完全拷贝父进程初始状态的内存空间，两者可以有一个只读的共享内存区
		- 在Windows系统中，父进程和子进程的内存空间在一开始就是不同的
		
 
> 注意：进程是一种抽象的过程

- 要先向操作系统发起请求，然后再在内存中创建对应空间，然后子进程才能运行，这需要一段时间，CPU不会等这段时间


## 进程终止和僵尸进程
- 进程终止
	- 正常退出：用户关闭交互式界面，或程序正常运行结束后的正常退出。
		- Linux中exit，Windows中exitprocess
	- 出错退出：目标文件不存在
		- python3  a.py      a.py 不存在
	- 严重错误退出:错误语法，非法指令
	- 被其他进程杀死
		- kill -9 

- 僵尸进程
	-  子进程结束后父进程会清理器内存，如果父进程在子进程结束之前就已经终止了，那么这个子进程就变成了一个僵尸进程


## 查看进程id
- windows
	- `tasklist`
	- `tasklist  |  findstr python`    筛选含有python的进程
		- findstr  也是一个单独的命令
		- | 用于过滤，即使用findstr命令过滤tasklist的结果
		- __两个命令在内存中是两个互相独立的内存空间，使用管道可以让两个进程共享数据__，此处共享的数据就是tasklist的结果
		- 这个命令可以简单看成，tasklist的结果放到管道 | 中，findstr再从管道中对数据进行处理
	- 此处管道不会堵塞是因为管道两侧分别只有一个进程 
	- 命令本质上就是一个脚本文件



## 互斥锁

> 同一台计算机上的所有进程都是共享一个操作系统的，所有进程都是通过向操作系统发出操作硬件之后才能使用硬件资源的

- 目的：
	- **控制同一时间内共享数据只能被一个任务所修改，保证数据的安全**
		- 当一个主进程下出现多个子进程的时候，如果这些子进程都需要对同一个硬件资源或者数据资源进行调用，比如有三个进程都要把不同的内容输出到打印机终端进行打印，如果三个进程并列对想操作系统发出操作打印机硬件的请求的话，在打印机终端上就会打印出三个进程的结果回合在一起的情况。这肯定不是我们想看到的。
- 互斥锁的作用就是把需要单独进行的进程锁起来，当这段进程运行结束，释放了锁之后，其他进程才能继续进行 。
	- 让进程有按顺序一个一个的进行，直到进程完毕锁释放下一个进程才能开始
- python中的锁
	- GIL锁
	- multiprocess中的Lock和RLock
	- threading中的Lock和RLock
	- 待补充
- 互斥锁的优缺点
	- 优点：增强了数据的安全性
	- 缺点：降低了程序运行的效率


## 进程中的其他属性

> 子进程运行结束之后，必须由父进程来完成子进程的清理工作，所以在正常程序运行中，当所有的子进程结束之后，父进程才能结束


- 守护进程
	- 不同于僵尸进程，主进程（父进程）运行结束的同时，会顺便关闭守护进程，防止僵尸进程的出现
	- python中，Process类的实例对象有一个daemon属性，在子进程start之前，设定该属性值为True就可以将该子进程变成守护进程

## 进程间通信

> 进程彼此之间是相互隔离的
> Windows系统中，子进程创建的时候是不会从父进程中复制任何数据的
> Linux系统中，子进程创建的时候会把父进程对应内存中的变量复制一份保存到自己的内存中，**是复制一份**，所以在子进程中对父进程中重名变量的修改不会对父进程中的变量产生任何作用

- 队列是基于管道和锁实现的
	- 先进先出
	- 数据传输效率高，保证了数据的安全
- 管道本质上是内存上一块共享空间为使用者提供了简单的get'、和put接口
	- 先进先出
	-  实现连个隔离的进程之间进行通信，但是**没有实现锁**
	-  数据传输效率高，但是数据在传输过程中由于没有锁导致多个进程同时使用同一个数据的时候会产生unexpected的结果，或者在多个进程同时使用一个硬件资源的时候，竞争性会导致硬件资源无法稳定运行
- 共享数据
	- 内存中开辟出一块空间用于存放数据，存放在该内存空间的数据可以被多个进程共享使用
	- multiprocessing.Manager类可以实现共享数据
- 文件效率低，没有锁
	- 文件保存在磁盘中，因为计算机上所有的进程都是共享同一个硬件资源，所以计算机中的进程可以共享同一个文件中的数据
	- IO阻塞时间太久，效率低，而且没有锁，数据也不安全

## 生产者消费者模型
- 概念
	- 生产者消费者模式是一种特殊的传输数据的方式。
	- 这种模式可以在两个进程之间实现传输数据
- 在多进程中，可以存在多个生产者，多个消费者
	- 可以看我在multiprocessing模块中的例子

- 存在于非并发多进程中的生产者消费者模型
	- 程序串行运行
	- 消费者**必须等生产者生产完之后，才能使用生产者生产出来的数据**

```python

def producer():
    return [i for i in range(10)]

def customer(obj):
    for i in range(len(obj)):
        res = obj.pop()
        print(res)

l = producer()
customer(l)
```


- 并发多线程中的生产者消费者模型
	- 进程可以并行存在
	- 生产者和消费者之间不存在阻塞，生产者只需要生产，消费者只需要从第三方共享空间中获取数据即可
	- 由阻塞变成了非阻塞的模式


```python

from multiprocessing import Process, JoinableQueue

def producer(q):
    for i in range(10):
        q.put(i)
    q.join()



def customer(q):
    while True:
        res = q.get()
        print(res)
        q.task_done()

if __name__ == '__main__':
    q = JoinableQueue(10)
    p = Process(target=producer, args=(q, ))
    c = Process(target=customer, args=(q, ))
    p.start()
    c.start()
    p.join()
    print('这是主进程')


```

## IPC(inter-process communication ):
- 队列（主要用队列）
	- multiprocessing.Queue
	- multiprocessing.JoinableQueue
	- **待补充(concurrent.futures)**
- 管道（不推荐使用，了解即可）
	- multiprocessing.Pipe
	- subprocess.PIPE
	- **待补充**



## 除了IPC之外，还有常见的其他方式


- 共享数据（又叫共享内存）
	- 可以共享字典，共享列表等（利用共享内存方式实现，没有锁）
	- multiprocessing.Manager
- 文件（效率低）





## 什么是线程
> 进程将程序运行过程中需要用到的资源整合到了一起，是一个资源单位
> **进程和线程都是一个程序运行的过程的抽象**

- 线程是cpu上的执行单位，每一个进程可以有一个主线程和多个其他线程
	- 主线程：操作系统中的每一个进程都会对应一个地址空间。每一个进程中都会默认有一个控制线程

## 线程与进程
- 同一个进程内的所有线程共用进程内存空间中的数据
	- 因此进程之间对共享资源具有竞用性 
- 一个父进程的多个子进程之间的内存空间是分割开的，而一个进程中的多个线程是可以共享该进程内的资源的
- **由于GIL锁的存在，一个进程下的多个线程之间是串行的**

```
1. Threads share the address space of the process that created it; processes have their own address space.
Threads have direct access to the data segment of its process; processes have their own copy of the data segment of the parent process.
2. Threads can directly communicate with other threads of its process; processes must use interprocess communication to communicate with sibling processes.
3. New threads are easily created; new processes require duplication of the parent process.
4. Threads can exercise considerable control over threads of the same process; processes can only exercise control over child processes.
5. Changes to the main thread (cancellation, priority change, etc.) may affect the behavior of the other threads of the process; changes to the parent process does not affect child processes.

```


## 线程的优点(重要)
- 创建的开销小
	- 直接在已有的进程的内存空间中进行一个线程
- 可以直接使用进程中的资源

- process.start()   与   thread.start()
> 两者的start都是要通过操作系统，但是thread要远远快于process
	- process慢，所以先进行主进程
	- thread快，会在主进程运行前运行

> 线程中使用os.getpid()得到的是线程所在进程的id


## 多线程
- 一个进程中开启多个线程，即多个任务共用一块地址空间，必须在一个进程内开启多个线程
	1. 多线程共享一个进程的地址空间
	2. 线程比进程更轻量级，线程比进程更容易创建和可撤销，创建start一个线程的时间要远远小于创建start一个进程的时间。



## 线程应用
>  基于其特点多个线程共享同一地址空间的资源

- 举个例子

```python
# 模拟实现文件内容修改，文件存取，文件格式化的多线程
from threading import Thread

my_list = []
def my_write():
    while True:
        msg = input('>>>').strip()
        if not msg:continue
        my_list.append(msg)

my_format_list = []
def my_format():
    while True:
        if my_list:
            res = my_list.pop().upper()
            my_format_list.append(res)

def flush_to_file():
    with open('text.txt', 'a') as af:
        while True:
            if my_format_list:
                res = my_format_list.pop()+'\n'
                af.write(res)
                af.flush()

if __name__ == '__main__':
    write_thread = Thread(target=my_write)
    format_thread = Thread(target=my_format)
    flush_thread = Thread(target=flush_to_file)
    write_thread.start()
    format_thread.start()
    flush_thread.start()

```


## 线程的两种创建方式
- 使用threading.Thread()
	- windows 系统中要在  if __name__ == '__main__'下


- 自定义类，但必须继承自Thread
	- 这种方式开启线程不需要在  if __name__ == '__main__'下

```python
from threading import Thread

class MyThread(Thread):
    def __init__(self, name):
        super().__init__()
        self.name = name

    def run(self):
        print('当调用线程的start方法的时候，自动调用run()方法')
        self.f()
        self.g()

    def f(self):
        pass
    def g(self):
        pass

m = MyThread('ziawang')
m.start()
-------------------------------------------------------------------
当调用线程的start方法的时候，自动调用run()方法

Process finished with exit code 0
```



## 共享内存

```python
# 线程共享内存
from threading import Thread, currentThread

n = 10
def task():
    print('%s running ....'%currentThread().getName())
    global  n
    n -= 1
    print('%s done . . .'%currentThread().getName())
    return n


if __name__ == '__main__':
    for i in range(10):
        t = Thread(target=task, name=str(i))    # 数字0不能当作线程名，所以用str()
        t.start()
    print(n)

```

```python
# 进程是一个独立的内存
from multiprocessing import Process
import os

n = 10
def task():
    print('%s is running '%os.getpid())
    global  n
    n -= 1
    print(n)										# 查看每一个进程中的n
    print('%s is done . . .'%os.getpid())


if __name__ == '__main__':
    p_l = []
    for i in range(10):
        p = Process(target=task)
        p.start()
        p_l.append(p)
    for i in p_l:
        i.join()
    print(n)
--------------------------------------------------------------
6852 is running 
9
6852 is done . . .
8968 is running 
9
8968 is done . . .
8940 is running 
9
8940 is done . . .
6020 is running 
9
6020 is done . . .
2412 is running 
9
2412 is done . . .
6040 is running 
9
6040 is done . . .
6644 is running 
9
6644 is done . . .
6520 is running 
9
6520 is done . . .
7696 is running 
9
7696 is done . . .
5784 is running 
9							# 每一个线程中的n的初始值都是10
5784 is done . . .
10							# 主进程中的n并没有变

Process finished with exit code 0


```


## 守护线程
- 在多进程中
	- 主进程**代码运行完毕**之后（主进程代码运行结束并不代表主进程结束），就会销毁守护进程，接着等非守护进程运行完毕之后，主进程才能结束（因为主进程的一个附加额外职责就是为其子进程收尸）
	- 主进程等待子进程是因为主进程要为运行完的子进程进行垃圾回收

- 在多线程中
	- **The significance of this flag is that the entire Python program exits when only daemon threads are left**
	- 主线程会等待所有非守护线程运行完毕之后，此时如果守护进程还在运行，那么就会销毁守护线程
		- 主线程随着进程的创建而出现。所以一个进程中主线程存在就代表了这个进程的存在，当进程中的主线程结束的时候，操作系统就会将该进程回收
	- 主线程等待其他线程的原因是主线程的存在保证了其他线程的稳定运行 
	

## GIL——Global Interpreter Lock
- GIL
	-  本质就是一个互斥锁，所有的互斥锁都是把并发运行切换到串行运行
	- In CPython, the global interpreter lock, or GIL, is a mutex that prevents multiple native threads from executing Python bytecodes at once. 
	- This lock is necessary mainly because CPython’s memory management is not thread-safe. (However, since the GIL exists, other features have grown to depend on the guarantees that it enforces.)
	- 在Cpython解释器中，同一个进程下开启的多线程，同一时刻只能有一个线程执行，无法利用多核优势


> 命令行python解释器运行文件实际上是开了一个进程，这个进程里存在多个线程


- 作用
	- **保证python解释器同一时间只能执行一个任务的代码**，将线程对Cpython解释器功能的并发竞用（伪并发）转换成串行运行
		1.   每一次用python解释器运行程序(python  test.py)，都会创建一个独立的进程，在这个进程内不仅有test.py的主线程，还有由该主线程开启的其他线程包括解释器级别的垃圾回收线程等其他线程，这些线程都运行在同一个进程中。因此该进程内的所有资源包括代码都是共享的，包括解释器的功能（解释器的功能其实就是一个个的代码块，存放了其他线程所需要的功能资源，比如垃圾回收，赋值，创建变量，甚至包括用户自定义的互斥锁等）
		2.   所有的线程想要运行自己的任务，都必须先拿到使用解释器功能的权限，即需要先获取GIL锁。每一个线程的任务，都需要把任务的代码当作参数传递给解释器的代码去执行。
		3. 多线程运行流程（）
			- Cpython中，脚本中的每一段代码都要经过解释器解析语法结构后才能运行，所以在没有GIL锁的情况下： 每创建的一个线程，都要先竞争抢到解释器的使用权(垃圾回收线程，主线程，新创建的线程)，这会导致解释器功能不稳定
			- 所以要对解释器功能进行加GIL锁。当一个线程得到解释器的功能的使用权限时，GIL.acquire()，如果这个线程阻塞了，那么这个线程就要交出这把锁，其他获取锁的线程使用解释器功能这个资源。该线程结束的时候，释放锁  GIL.release()
			- 因此，在Cpython中，线程虽然开销小，但是无法利用多核优势，因为线程本质上就是串行的，进程虽然可以利用多核优势，但是开销大__然而多核对IO阻塞也每什么卵用（加黑色一个cpu处理四个sleep线程，来回切但并没计算）__
	- 给线程获取解释器的执行权限加GIL锁
		- 多个线程运行的时候，谁先抢到这把锁，会就可以先进行，同时保证了在使用的过程中数据的安全。
		- 如果占用GIL锁的线程IO阻塞时间较长，就会自动释放该锁，先交给其他线程，这样轮流替换，实现了伪线程并发的效果


- GIL与Lock
	- 由上可知，GIL锁保证了进程中解释器级别数据的安全，但是对于进程内用户创建的数据，（比如某个变量）就需要用户自己使用Lock进行加锁处理
	- 对于线程共享的数据（应用软件级别的数据）：GIL锁并不会起作用，GIL的锁加在了cpython解释器上，但是多个线程对线程共享数据的竞争调用仍然存在的，所以保护不同的数据要加上不同的锁
	- 举个栗子

```python
n = 100

def task():
    global n
    temp = n
    time.sleep(0.5)			# 这个I/O阻塞时间越短，得到的n会越小，详情看下面解释
    n = temp - 1


if __name__ == '__main__':
    t_l = []
    for i in range(100):
        t = Thread(target=task)
        t_l.append(t)
        t.start()
    for t in t_l:
        t.join()
    print(n)
--------------------------------------------
99

Process finished with exit code 0
```

1. 每一个线程想要进行其任务都要先获得GIL锁
2. 获取到GIL锁的线程运行中如果遇到了I/O阻塞，根据多道技术中CPU时间上的复用性原理，CPU就会释放该阻塞任务的GIL锁，交给下一个获取到GIL锁的任务执行。同时，由于I/O阻塞被释放锁的线程会记住当时的状态，并在下次获取GIL锁的时候继续从该位置开始往下进行
3. 以次类推，上述线程的任务task中，每一个线程都会阻塞，在这个阻塞过程中，完全可以让这100个线程中的所有temp变量都得到一个值100，因此他们都保存了temp变量值100。
4. cpu会在这些线程之间不断来回快速切换，先结束阻塞的线程会先执行任务，即n = temp-1
5. 最终n得到99
6. 注意：关于阻塞时间的控制，线程开销小，运行非常快，所以时间越短，再第一个线程开始任务的阻塞时间内，不足以所有100个线程的temp都得到100这个值，所以会得到修改后的n值，不信你自己试试

- 对于这种情况，用户在进程中创建的数据明显是不安全的，需要我们手动上锁

```python
# Lock
from threading import Thread, Lock
import time


n = 100

def task():
    mutex.acquire()
    global n
    temp = n
    time.sleep(0.5)
    n = temp - 1
    mutex.release()

if __name__ == '__main__':
    mutex = Lock()
    t_l = []
    for i in range(100):
        t = Thread(target=task)		# 线程共享进程中的资源，因此不许要在传入参数
        t_l.append(t)
        t.start()
    for t in t_l:
        t.join()
    print(n)
----------------------------------------------------------
0

Process finished with exit code 0
```

- 抢到GIL锁的线程会继续运行加了mutex锁的代码，这样会保证数据的安全





- GIL与多线程
	- 一个进程内的多个线程想要执行代码中的任务，首先要抢到的是GIL锁，再将代码当作参数传递给解释器，由解释器执行。所以一个进程内的所有线程都是串行着来的，同一时间只能由一个线程使用cpython解释器，这样就给垃圾回收机制等其他线程提供了余地

- 多核与多线程
	- GIL锁将Cpython解释器的功能资源保护了，使得多个线程只能一个一个的运行。那么问题来了，这种情况Cpython解释器就无法利用计算机的多核优势了，因为同一时间Cpython解释器功能只能由一个线程的任务去使用。
	- 多核CPU有什么优势？
		- 密集运算中
			- 多核CPU可以同时处理多个运算，即多核提升的是计算机的计算性能
		- 密集I/O
			- __多核对密集I/O并没由什么乱用__
			- 每一个cpu一旦运行到的阻塞，从多道技术我们可以直到，此时CPU会切换到其它任务上，等I/O阻塞完成之后CPU才能对其进行运算，因此不管有几个CPU，对计算机的I/O阻塞都是没有任何影响的
			- 注意：随着CPU核数的增多，其实执行效率是会提高的，这是因为一个程序基本上不会是纯计算或者纯I/O
	- 对于密集运算的程序，应采用多进程
		- 金融分析
	- 对于密集I/O的程序，应使用多线程
		- socket，爬虫，web


```python
# 模拟密集运算
from multiprocessing import  Process
from threading import  Thread
import  time


def task():
    s = 0
    for i in range(10000000):
        s += i


if __name__ == '__main__':
    start = time.time()
    p_l = []
    for i in range(4):
        # p = Process(target=task)    # 2.2261252403259277
        p = Thread(target=task)     # 4.479249954223633
        p_l.append(p)
        p.start()
    for p in p_l:
        p.join()
    print(time.time()-start)

```


```python
# 模拟I/O运算
from multiprocessing import  Process
from threading import  Thread
import time

def task():
    time.sleep(2)

if __name__ == '__main__':
    start = time.time()
    p_l = []
    for i in range(100):
        # p = Process(target=task)        # 7.26143217086792
        p = Thread(target=task)     # 2.0261619091033936
        p_l.append(p)
        p.start()
    for p in p_l:
        p.join()

    print(time.time()-start)

```



## 并发多进程程，串行多线程
- Cpython的GIL特性导致多线程是虽然运行速度快，但是它仍然是伪并发，本质上是串行
- 多进程是并发的

```python
# 串行多线程，不用join()
from threading import Thread,get_ident
import time


def task():
    print('%s task running . . .'%get_ident())
    time.sleep(0.5)
    print('%s task done. . . .'%get_ident())

if __name__ == '__main__':
    start = time.time()
    for i in range(5):
        t = Thread(target=task)
        t.start()
    print('总共运行了:%s'%(time.time()-start))
-----------------------------------------------------------------------
# 不用join()结果
22464 task running . . .
22448 task running . . .
10316 task running . . .
6924 task running . . .
19940 task running . . .
总共运行了:0.0019998550415039062		# 这样得到的结果是不准确的
22464 task done. . . .
22448 task done. . . .
6924 task done. . . .
10316 task done. . . .
19940 task done. . . .

Process finished with exit code 0
```

> 不用join()，CPU遇到阻塞，会运行主线程

```python
# 用join()，注意，每一个线程都要join()，确保主线程在所有其他线程运行完再运行
D:\Python36\python.exe D:/files/python_practice/20170828-0903/0830/onlcass.py
22860 task running . . .
14416 task running . . .
10408 task running . . .
19280 task running . . .
13632 task running . . .
22860 task done. . . .
13632 task done. . . .
19280 task done. . . .
10408 task done. . . .
14416 task done. . . .
总共运行了:0.5022890567779541

Process finished with exit code 0
```

- 从上面可以看出，多线程确实是串行完成任务的，但是多个线程运行时如果遇到了阻塞，CPU就会切换到其它线程上，这种快速的切换就将线程阻塞的时间完全利用起来，直到所有线程都阻塞，cpu等待最快结束阻塞的线程，并执行其中的任务，因此cpu实际上只阻塞了0.5秒多一点，可以比较看没有阻塞的栗子

```python
# 多线程中没有阻塞
from threading import Thread,get_ident
import time


def task():
    print('%s task running . . .'%get_ident())
    # time.sleep(0.5)
    print('%s task done. . . .'%get_ident())

if __name__ == '__main__':
    start = time.time()
    t_l = []
    for i in range(5):
        t = Thread(target=task)
        t_l.append(t)
        t.start()
    for t in t_l:
        t.join()
    print('总共运行了:%s'%(time.time()-start))
--------------------------------------------------------------------------------------
14268 task running . . .
14268 task done. . . .
26264 task running . . .
26264 task done. . . .
27020 task running . . .
27020 task done. . . .
8860 task running . . .
8860 task done. . . .
11856 task running . . .
11856 task done. . . .
总共运行了:0.002500295639038086

Process finished with exit code 0

```


## 多线实现套接字
- 进程和线程不能无限开


## Lock与join
- Lock更灵活，只锁住共享数据修改的部分

```python
from threading import Thread, Lock, get_ident
import time


def task():
    time.sleep(1)
    mutex.acquire()
    print('%s running . . . '%get_ident())
    time.sleep(0.5)
    print('%s done . . . '%get_ident())
    mutex.release()


if __name__ == '__main__':
    start = time.time()
    mutex = Lock()
    t_l = []
    for i in range(10):
        t = Thread(target=task)
        t_l.append(t)
        t.start()
    for t in t_l:
        t.join()
    print('总共用时：%s'%(time.time()-start))
----------------------------------------------------------------------------------
27136 running . . . 
27136 done . . . 
14492 running . . . 
14492 done . . . 
14532 running . . . 
14532 done . . . 
24084 running . . . 
24084 done . . . 
26992 running . . . 
26992 done . . . 
18784 running . . . 
18784 done . . . 
19104 running . . . 
19104 done . . . 
23716 running . . . 
23716 done . . . 
7196 running . . . 
7196 done . . . 
9604 running . . . 
9604 done . . . 
总共用时：6.01282262802124

Process finished with exit code 0

```

- join方法把任务整体锁起来了，对于非保护数据对象也会进行保护，降低程序的运行效率


```python
from threading import Thread, Lock, get_ident
import time


def task():
    time.sleep(1)		# 这个阻塞时间用来代表不需要保护的数据部分
    print('%s running . . . '%get_ident())
    time.sleep(0.5)		# 这个表示要保护的数据部分
    print('%s done . . . '%get_ident())


if __name__ == '__main__':
    start = time.time()
    for i in range(10):
        t = Thread(target=task)
        t.start()
        t.join()
    print('总共用时：%s'%(time.time()-start))
------------------------------------------------------------------------------
14476 running . . . 
14476 done . . . 
13072 running . . . 
13072 done . . . 
11660 running . . . 
11660 done . . . 
19620 running . . . 
19620 done . . . 
19432 running . . . 
19432 done . . . 
17060 running . . . 
17060 done . . . 
7748 running . . . 
7748 done . . . 
25604 running . . . 
25604 done . . . 
1124 running . . . 
1124 done . . . 
16084 running . . . 
16084 done . . . 
总共用时：15.034180402755737

Process finished with exit code 0
```


## 死锁与递归锁
- 死锁
	- 两个或两个以上的进程或线程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去。此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程称为死锁进程，如下就是死锁
	- 在一组进程（或线程）中的每一个进程（或线程）都占有一个不会释放的资源，但是因为互相申请被其他进程（或线程）所占用不会释放的资源而处于一种永久等待的状态
	- 死锁的四个条件
		1. 互斥条件：资源不能被共享，只能由一个进程使用
		2. 请求与保持条件：已经得到资源的进程可以再次申请新的资源
		3.  非剥夺资源：已经分配的资源不能从相应的进程中被强制地剥夺
		4.  循环等待条件：系统中若干进程组成环路，该环路中没一个进程都在等待相邻进程正在占用的资源

```python
from threading import Thread, Lock,currentThread
import time


mutexA = Lock()
mutexB = Lock()

class MyThread(Thread):
    def run(self):
        self.f()
        self.g()

    def f(self):
        mutexA.acquire()
        print('%s acquire the lock  A'%currentThread().getName())
        mutexB.acquire()
        print('%s acquire the lock  B' % currentThread().getName())
        mutexB.release()
        print('%s release the lock  B' % currentThread().getName())
        mutexA.release()
        print('%s release the lock  B' % currentThread().getName())

    def g(self):
        mutexB.acquire()
        print('%s acquire the lock  B' % currentThread().getName())
        time.sleep(2)
        mutexA.acquire()
        print('%s acquire the lock  A' % currentThread().getName())
        mutexA.release()
        print('%s release the lock  A' % currentThread().getName())
        mutexB.release()
        print('%s release the lock  B' % currentThread().getName())

t1 = MyThread(name='t1')
t2 = MyThread(name='t2')
t3 = MyThread(name='t3')

t1.start()
t2.start()
t3.start()

---------------------------------------------------------------------------
t1 acquire the lock  A
t1 acquire the lock  B
t1 release the lock  B
t1 release the lock  B
t2 acquire the lock  A
t1 acquire the lock  B
# 卡在了这里
```

- 递归锁（可重入锁）
	- python中为了支持在同一个线程中多次请求统一资源，python提供了递归锁
	- threading.RLock()
		- 在一个RLock的内部存在着两个变量lock和count。当count的计数为0的时候，锁被释放，然后其他线程才能获得该锁
		- count记录的是每一个递归锁的使用情况
			- 每一次acquire，count计数都会增加1
			- 每一次release，count计数都会减1
		- 保证了一个资源可以被多次acquire，直到一个线程所有 的acquire都被release，其他线程才能获得递归锁，进而访问资源。

```python
from threading import Thread,currentThread, RLock
import time


mutexA = mutexB = RLock()			# 链式赋值

class MyThread(Thread):
    def run(self):
        self.f()
        self.g()

    def f(self):
        mutexA.acquire()
        print('%s acquire the lock  A'%currentThread().getName())
        mutexB.acquire()
        print('%s acquire the lock  B' % currentThread().getName())
        mutexB.release()
        print('%s release the lock  B' % currentThread().getName())
        mutexA.release()
        print('%s release the lock  B' % currentThread().getName())

    def g(self):
        mutexB.acquire()
        print('%s acquire the lock  B' % currentThread().getName())
        time.sleep(2)
        mutexA.acquire()
        print('%s acquire the lock  A' % currentThread().getName())
        mutexA.release()
        print('%s release the lock  A' % currentThread().getName())
        mutexB.release()
        print('%s release the lock  B' % currentThread().getName())

t1 = MyThread(name='t1')
t2 = MyThread(name='t2')
t3 = MyThread(name='t3')

t1.start()
t2.start()
t3.start()

-------------------------------------------------------------------------------------------------
t1 acquire the lock  A
t1 acquire the lock  B
t1 release the lock  B
t1 release the lock  B
t1 acquire the lock  B
t1 acquire the lock  A
t1 release the lock  A
t1 release the lock  B
t2 acquire the lock  A
t2 acquire the lock  B
t2 release the lock  B
t2 release the lock  B
t3 acquire the lock  A
t3 acquire the lock  B
t3 release the lock  B
t3 release the lock  B
t2 acquire the lock  B
t2 acquire the lock  A
t2 release the lock  A
t2 release the lock  B
t3 acquire the lock  B
t3 acquire the lock  A
t3 release the lock  A
t3 release the lock  B

Process finished with exit code 0
```

> 关于递归锁，更详细了看我在[threading模块](http://www.ziawang.com/python/basic_knowledge_of_python/threading.html)的介绍




## 线程同步——信号量
本质上就是多个锁，可以控制最大线程数





## 线程同步——event事件
event.wait()
event.set()
event.isSet()
event.clear()
应用场景
MySQL中进程等待数据库开启，数据库开启e.set()，进程就可以连接上MySQL

def conn_mysql():
	pass

def check_mysql():
	pass


## 定时器




## 线程queue
队列：实现了生产者和消费者之间的解耦




##进程池和线程池




## windows   linux
2.不同的是：在UNIX中，子进程的初始地址空间是父进程的一个副本，提示：子进程和父进程是可以有只读的共享内存区的。但是对于windows系统来说，从一开始父进程与子进程的地址空间就是不同的。
global  n
global n


