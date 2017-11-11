# 线程
- 线程也是程序运行过程中的一个抽象，他是CPU上的执行单位。一个进程可以有多个线程
- 主线程：操作系统中的每一个进程都会对应一个地址空间。每一个进程中都会默认有一个控制线程	- 主线程随着进程的创建而出现。所以一个进程中主线程存在就代表了这个进程的存在，当进程中的主线程结束的时候，操作系统就会将该进程回收

## 线程与进程
- 区别
	- 同一进程下，多个线程共用进程中的资源，因此线程之间对进程下的数据具有竞用性
	- 由于GIL锁的存在，同一进程下，同一时刻只能有一个线程在进行
	- 同一父进程下，多个子进程之间的内存空间相互隔离，无法直接共享数据
	- 同一父进程下，多个子进程可以并发进行

> 两者的start都是要通过操作系统，但是thread要远远快于process  
> process慢，所以先进行主进程    
> thread快，会在主进程运行前运行   

```
# 官方原文
1. Threads share the address space of the process that created it; processes have their own address space.
Threads have direct access to the data segment of its process; processes have their own copy of the data segment of the parent process.
2. Threads can directly communicate with other threads of its process; processes must use interprocess communication to communicate with sibling processes.
3. New threads are easily created; new processes require duplication of the parent process.
4. Threads can exercise considerable control over threads of the same process; processes can only exercise control over child processes.
5. Changes to the main thread (cancellation, priority change, etc.) may affect the behavior of the other threads of the process; changes to the parent process does not affect child processes.

```

## python的GIL锁
> 所有的互斥锁都是把并发运行切换到串行运行

- GIL锁本质上也是一个互斥锁，只不过它保护的是全局解释器和环境状态变量，使同一进程下同一时刻只能存在一个线程
	- 官方解释
	- **In CPython, the global interpreter lock, or GIL, is a mutex that prevents multiple native threads from executing Python bytecodes at once.**
	- **This lock is necessary mainly because CPython’s memory management is not thread-safe.** (However, since the GIL exists, other features have grown to depend on the guarantees that it enforces

- 我对GIL的理解
	1. 每次python解释器运行程序(python test.py)，都会创建一个独立的进程
	2. 这个进程内不仅有test.py的主线程，还有由该主线程开启的其他线程包括解释器级别的垃圾回收线程等其他线程，这些线程都运行在同一个进程中。因此该进程内的所有资源包括代码都是共享的，包括解释器的功能（解释器的功能其实就是由一个个的代码块构成，存放了其他线程所需要的功能资源，比如垃圾回收，赋值，创建变量，甚至包括用户自定义的互斥锁等）
	3. 所有的线程想要运行自己的任务，都必须先拿到使用解释器功能的权限，即需要先获取GIL锁。当一个线程得到解释器的功能的使用权限时，GIL.acquire()，如果这个线程阻塞了，那么这个线程就要交出这把锁，其他获取锁的线程使用解释器功能这个资源。该线程结束的时候，释放锁 GIL.release()
#### GIL全局解释器锁与用户的互斥锁
- GIL只保证了解释器级别资源的安全，不能保证线程内用户创建的数据的安全。因此，需要用户自己创建互斥锁进行保护	
	- 多个线程对线程共享数据的竞争调用仍然存在的

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

```
```
1. 每一个线程想要进行其任务都要先获得GIL锁
2. 获取到GIL锁的线程运行中如果遇到了I/O阻塞，根据多道技术中CPU时间上的复用性原理，CPU就会释放该阻塞任务的GIL锁，交给下一个获取到GIL锁的任务执行。同时，由于I/O阻塞被释放锁的线程会记住当时的状态，并在下次获取GIL锁的时候继续从该位置开始往下进行
3. 以次类推，上述线程的任务task中，每一个线程都会阻塞，在这个阻塞过程中，完全可以让这100个线程中的所有temp变量都得到一个值100，因此他们都保存了temp变量值100。
4. cpu会在这些线程之间不断来回快速切换，先结束阻塞的线程会先执行任务，即n = temp-1
5. 最终n得到99
6. 注意：关于阻塞时间的控制，线程开销小，运行非常快，所以时间越短，再第一个线程开始任务的阻塞时间内，不足以所有100个线程的temp都得到100这个值，所以会得到修改后的n值，不信你自己试试
```

```python
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
```


## 创建线程
- [threading]()
- [concurrent.futures]()


## 线程的优点
- 创建的开销小，远远小于创建一个进程的开销
	- 线程比进程更轻量级
	- 线程比进程更容易创建和可撤销
	- 创建start一个线程的时间要远远小于创建start一个进程的时间。
- 可以直接使用进程中的资源

## 守护线程
- 与守护进程不同。主线程会等待所有非守护线程运行完毕之后，此时如果守护进程还在运行，那么就会销毁守护线程
	- 原因是因为线程之间是共享进程内的数据的，如果终止了守护线程，可能会对其他线程的执行产生影响
	- 官方原话**The significance of this flag is that the entire Python program exits when only daemon threads are left**





## 多线程的效率问题
#### 多核与多线程
- GIL使得python多线程无法充分发挥多核优势，同一时间下只能有一个线程在进行
- 多核的优势
	- 对于计算密集型的任务，可以极大提高效率
		- 金融分析，算法，数据处理等
	
- 多核与IO
	- 对于IO密集型的任务，多核并没有什么卵用。因为CPU一旦遇到了阻塞，就会挂起当前任务，屏切换到其他任务上。
	
>  随着CPU核数的增多，其实执行效率是会提高的，这是因为一个程序基本上不会是纯计算或者纯I/O


#### 多线程处理IO密集型任务
- 创建一个线程的开销要远小于进程，线程之间的来回切换所需的时间必然也要远少于进程，进程作为资源单位，使用多进程处理IO密集型的任务时，肯定没有多线程高效
	- socket，爬虫，web

> 但是对于计算密集型的任务，还是要选择多进程


## 死锁与递归锁(python)
#### 死锁
> 在一组进程（或线程）中的每一个进程（或线程）都占有一个不会释放的资源，但是因为互相申请被其他进程（或线程）所占用不会释放的资源而处于一种永久等待的状态

- 两个或两个以上的进程或线程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去。此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程称为死锁进程

#### 死锁的四个条件
1. 互斥条件：资源不能被共享，只能由一个进程使用（互斥锁）
2. 请求与保持条件：已经得到资源的进程可以再次申请新的资源
3. 非剥夺资源：已经分配的资源不能从相应的进程中被强制地剥夺
4. 循环等待条件：系统中若干进程组成环路，该环路中没一个进程都在等待相邻进程正在占用的资源

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
```

#### 递归锁
- 为了支持在同一个线程中多次请求同一资源，python提供了递归锁
- [递归锁]()
