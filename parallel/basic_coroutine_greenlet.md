## greenlet 与 yield
- 通过yield生成器可以实现在不同任务之间进行来回切换，虽然产生了协程的效果，但是并不是真正的协程。greenlet是真协程，它是python的一个C扩展，提供可自行调度的微线程(dummy)即协程
- greenlet的target.switch(value)可以从当前任务切换到其他指定的任务（target），然后返回值。

#### 注意
- **greenlet虽然实现了在不同任务之间的切换，但是并没又解决遇到IO切换的效果。但是gevent实现了**

## Module_level Function
- greenlet.getcurrent()
	- Returns the current greenlet
	- the one which called this function

- greenlet.GreenletExit
	-  used to kill a single greenlet.
	-  **This special exception does not propagate to the parent greenlet**

> switch      开关    

## greenlet.greenlet object
- greenlet.greenlet(run=None, parent=None)
	- return a greenlet object
	- run: the callable object to invoke
	- parent: the parent greenlet, which defaults to the current greenlet

## greenlet objects Methods

- g.switch(*args, **kwargs)
	- Switches execution to the greenlet g, sending it the given arguments.
	- if g did not start yet, then it will start to run now.
	- switch函数在调用的时候，如果callable对象有参数，那么第一次switch传参之后，之后再调用该greenlet对象的switch都不需要传参了
		- 第二次传入了不同的参数
	- 一个比较常用的写法`receive_value = g1.switch(x)`执行整个语句时，会有以下两个步骤
		1. 执行`g1.switch(x)`，切换到其他任务
		2. 当前任务被其对应的greenlet对象g2.switch(value)再次唤起，将value赋予receive_value，并继续执行任务。

```python
from greenlet import greenlet

def task1(x, y):
    print('task1  running : %s + %s = %s' % (x, y, x+y))
    recv = t2.switch(1)                 
    print('receive a value : %s' % recv)

def task2(n):
    print('task2 running : %s' % n)
    t1.switch('hello')

t1 = greenlet(run=task1)
t2 = greenlet(run=task2)
t1.switch(1, 2)

```

#### 注意
- 只有当greenlet对象对应的函数执行完毕，该greenlet协程才会die。

```python
from greenlet import  greenlet

def f():
    print('f1')
    g.switch()
    print('f2')

def g():
    print('g1')
    f.switch()
    print('g2')

f = greenlet(f)
g = greenlet(g)
f.switch()
print('f die? %s'%f.dead, 'g die ? %s'%g.dead)
g.switch()
print('f die? %s'%f.dead, 'g die ? %s'%g.dead)
```

- 当刚创建一个greenlet 对象的时候，首先初始化一个空的栈，当该greenlet对象调用switch方法的时候，会先运行构造greenlet对象时传入的函数（callable对象），如果这个函数中存在其他greenlet对象对switch方法的调用，那么当前这个协程就会被挂起，并保存运行状态，等再次调用其对应greenlet对象的时候切换回来。
- **当一个协程对应函数执行完毕，那么这个协程就变成dead状态**，如果一个协程被挂起后，没有再次被switch唤起，就会发生数据泄露


## greenlet并不能实现遇到IO切换

```python
def f(x):
    print('f is runinng')
    time.sleep(1)
    g.switch(1)
    print('f is done')
    g.switch(2)


def g(x):
    print('g is running')
    time.sleep(1)
    f.switch(1)
    print('g is done')


start = time.time()
f = greenlet(run=f)
g = greenlet(run=g)
f.switch(1)
print('total time : %s' % (time.time()-start)) 
```


## 数据溢出
- greenlet对象没有被正常关闭的时候，

```python
from greenlet import greenlet, GreenletExit

l = []

def show_leak():
    def test1():
       g2.switch()

    def test2():
        l.extend([i for i in range(100)])
        g1.switch()
        print('finish switch del l')
        del l[:]						  # g1结束，没有切换回g2，l没有被清空

    g1 = greenlet(test1)
    g2 = greenlet(test2)
    g1.switch()
	g1 = g2 = None
	print('length of l: %s' % len(l))     # 100
	

if __name__ == "__main__"：
	show_leak()
```

- 解决数据溢出的方法就是利用GreenletExit异常。如果一个greenlet对象的引用计数变成了0，那么就在它上次挂起的地方抛出Greenletxit异常，通过try分支来处理泄露

```python
from greenlet import greenlet, GreenletExit

l = []


def show_leak():
    def task1():
        g2.switch()

    def task2():
        l.extend([i for i in range(100)])
        try:
            g1.switch()
        finally:
            print('finish switch del l')
            del l[:]

    g1 = greenlet(task1)
    g2 = greenlet(task2)
    g1.switch()
    g1 = g2 = None			# g1, g2指向的greenlet对象引用计数变成0
    print('length of l : %s' % len(l))


if __name__ == '__main__':
    show_leak()
```


## greenlet.parent
> 是一个属性

- 每一个协程都有一个父协程，父协程默认是创建该协程的协程，当前协程结束之后会回到父协程中执行。