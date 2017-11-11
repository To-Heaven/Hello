# 进程

## 从操作系统说起
#### 操作系统的两大功能
1.  隐藏复杂的各种硬件调用和复杂底层功能调用接口，为应用程序提供了简单易调用的功能接口
2.  将多个应用程序对硬件资源的竞态访问变得有序化
	- **进程之间的管理和协调调度是由操作系统完成的**


#### 并发与并行
- 多核CPU才能并行。
	- 并行进程数 = CPU核数

- 并发在单核和多核CPU上都能实现
	- 即使是多核CPU，其数量也无法满足计算机中程序对进程数的需求
	- 并发就是依靠多道技术来实现并行效果

- 并发的本质
	- CPU快速在任务之间快速来回切换
	- 每个被挂起的任务的状态要保存起来



- **实现并发的核心**
	- **操作系统保存每一个进程被挂起时的状态**
	- 操作系统有一个进程表，每一个进程占用的进程表项都会被记录进去。该表中存放有进程状态的重要信息，保证进程再其被CPU调用时能保持任务的连续性
		- 内存分配状况
		- 堆栈指针
		- 进程中打开文件的状态
		- 账号和调度信息
		- 程序计数器
		- 进程由运行态转换成就绪态或阻塞态时必须保存的信息。


#### 多道技术
> 对于单核来说，通过多道技术能够实现并发

- **空间上的复用**
	- 内存中可以同时存在多个程序，且程序之间必须实现物理级别上的隔离

-**时间上的复用** 
	- CPU可在各个程序之间来回快速地切换
	- CPU切换进程的两个条件		
		- 一个程序占用CPU资源时间过长
		- CPU处理的程序遇到了IO阻塞
			- CPU会挂起当前进程，切换到其他进程，直到该进程阻塞IO结束，才会再次被CPU调用




## 进程介绍
> 进程是一个抽象概念

- 进程是程序运行过程的抽象，它把一段程序运行时所需要的所有资源整个到一起，是一个资源单位
- 进程和程序的区别
	- 程序就是文件中的一堆代码，程序的**每一次执行都会创建一个新的进程**
	- 进程是程序运行过程的抽象

#### 进程的三种状态
	- 运行态
	- 就绪态
	- 阻塞态

#### 进程被挂起的两种原因
	- 自身层面
		- 操作系统为了保证CPU处于最大效率，处于阻塞态的进程被挂起
	- 操作系统层面
		- 进程占用CPU时间过多或优先级低与其他进程时，操作系统会暂时挂起该进程，并调用其他进程去使用CPU，保证处于就绪态的进程"雨露均沾"。

## 进程特点



## 创建进程
- [multiprocessing]()
- [concurrent.futures]()

#### 系统调用
- 所有创建新进程的方式都是基于操作系统的系统调用是实现的
- Linux中
	- 通过系统调用fork来创建子进程 
	- **创建的子进程在初始化的时候会复制父进程的内存空间，两者有相同的存储映像、同样打开的文件**
	
- Windows中
	- 通过系统调用createprocess来创建子进程
	

#### 不同操作系统进程间内存分配
- 相同点
	- 父进程和子进程有各自不同的地址空间（多道技术要求在物理层面实现进程之间内存的隔离），任何一个进程再其地址空间中的修改都不会影响到另一个进程

- 不同点
	- Windows中，父进程和子进程的内存空间在一开始就是不同的
	- Linux中，子进程完全拷贝父进程初始状态的内存空间，两者可以有一个只读的共享内存区
	
- 注意
	- python3中，有一个小数池优化，这里的小数池指的是简单的数据，对于简单的数据，比如比较小的整数，为了节省空间，多个因工会指向这同一个整数对象，这一点在父进程和子进程中也是适用的，因为这个小数池作为第三方内存，也实现了进程间数据的共享。
	- 看代码

```python
from multiprocessing import Process
import os


def f(a):
    print(a, os.getpid(), id(a))


if __name__ == '__main__':
    a = 50
    print(a, os.getpid(), id(a))

    p = Process(target=f, args=(a, ))
    p.start()
    p.join()
```
 
- 测试

```python
# Windows中
import os
from multiprocessing import Process


def func():
    print(a, '来自于 %s'%os.getpid())


if __name__ == '__main__':
    print('主进程%s开始'%os.getpid())

    a = 1

    func()

    p = Process(target=func)
    p.start()						# 报错，a未定义，windows系统中，子进程不会copy父进程中的内存空间（变量a）
```

```python
# windows中
from multiprocessing import Process, Lock

d = {'count': 10}							# 如果将d放在"if __name__ == __main__" 中，就会报错


class StockProcess(Process):
    filename = 'stock'

    def __init__(self):
        super().__init__()
        self.mutex = Lock()

    def run(self):
        self.last_count()
        with self.mutex:
            print(self.pid, d, id(d))		# 每一个进程内的d都是不同的
            self.get_stock()

    def last_count(self):
        print(d)
        print('%s surplus of stock' % d['count'])
        return d['count']

    def get_stock(self):
        if d['count'] > 0:
            d['count'] -= 1
            print('%s get stock successful' % self.pid)


if __name__ == '__main__':

    for i in range(50):
        s = StockProcess()
        s.start()
```


## 终止进程
- 正常退出：用户关闭交互式界面，或程序正常运行结束后的正常退出。
	- Linux： exit
	- Windows: exitprocess

- 出错退出
	- 比如`python3 a.py `a.py不存在
	
- 严重错误退出:错误语法，非法指令

- 被其他进程杀死
	-linux: kill -9 pid
		- `ps aux | grep python`
	- windows: taskkill \T \F \PID
		- tasklist | findstr python 筛选含有python的进程
			- `findstr` 也是一个单独的命令
			-  `|` 用于过滤
			-  两个命令在内存中是两个互相独立的内存空间，使用管道可以让两个进程共享数据，此命令使用`findstr`命令过滤`tasklist`的结果
> 命令本质上就是一个脚本文件

## 特殊的进程
#### 僵尸进程
- 父进程在子进程终止之后会清理子进程内存，如果父进程在子进程结束之前就已经终止了，那么这个子进程就变成了一个僵尸进程。因此，在正常程序运行中，所有的子进程结束之后，父进程才能结束

#### 守护进程
**当主进程代码执行完毕时，会终止守护进程，即使此时还有非守护子进程存在，人后主进程会等待非守护进程的子进程结束，执行清理工作**

## IPC
- 进程间的内存空间存在物理级别隔离，要想是进程间数据的传输，需要第三方对象来作为媒介。

####　队列
- 先进先出
- 内部实现了锁，保证了数据的安全

#### 管道
- 先进先出
- 没有实现锁，无法保证数据的安全
	- 多个进程通过没有锁的管道同时使用一个硬件资源的时候，竞争性会导致硬件资源无法稳定运行

#### 共享数据
- 内存中的一块空间用于存放可以被多个进程访问的共享数据
- 没有实现锁，无法保证数据安全

> 其实文件也可以实现数据的共享。但是效率更低，安全性更差。



## 互斥锁
> 同一台计算机上的所有进程共享一个操作系统，进程通过向操作系统发出操作硬件的系统调用之后才能使用硬件资源

- 作用
	- **保证数据的安全**，保证同一时间内共享数据只能被一个进程修改

- 优缺点
	- 优点：增强了数据的安全性
	- 缺点：降低了程序运行的效率

## 生产者消费者模型
#### 介绍
- 生产者消费者模式是一种传输数据的特殊模式。
	- 这种模式可以在两个进程之间实现传输数据

#### 并发与串行中的生产者消费者模型
- 串行情况	
	- **消费者必须等生产者生产完之后，才能使用生产者生产出来的数据**

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

- 并发情况
	- **生产者只需要生产，将生产出的数据存放到第三方IPC中，消费者只需要从第三方IPC中获取数据**
	- **由阻塞变成了非阻塞的模式**

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


