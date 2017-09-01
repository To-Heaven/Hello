http://greenlet.readthedocs.io/en/latest/

# greenlet 模块
## greenlet.greenlet object
- greenlet.greenlet(run=None, parent=None)
	- return a greenlet object
	- run: the callable object to invoke
	- parent: the parent greenlet, which defaults to the current greenlet

## Module_level Function
- greenlet.getcurrent()
	- Returns the current greenlet
	- the one which called this function

- greenlet.GreenletExit
	-  used to kill a single greenlet.
	-  **This special exception does not propagate to the parent greenlet**

> switch      开关    

## greenlet objects Methods

- g.switch(*args, **kwargs)
	- Switches execution to the greenlet g, sending it the given arguments.
	- if g did not start yet, then it will start to run now.
	- switch函数在调用的时候，如果callable对象有参数，那么第一次switch传参之后，之后再调用该greenlet对象的switch都不需要传参了
		- 第二次传入了不同的参数



```python
# 不存在IO阻塞的协程
from greenlet import greenlet
import time

def f(x):
    print('f  running . . .%s'%x)
    g.switch(1)
    print('f  done . . . %s'%x)
    g.switch(2)

def g(x):
    print('g  running . . .%s'%x)
    f.switch(2)
    print('g  done . . . %s'%x)

start = time.time()
f = greenlet(f)
g = greenlet(g)
f.switch(1)
end = time.time()
print('total time : %s'%(end-start))
----------------------------------------------------------------
f  running . . .1
g  running . . .1
f  done . . . 1
g  done . . . 1
total time : 0.0

Process finished with exit code 0
```



```python
# 存在IO阻塞的协程
from greenlet import greenlet
import time

def f(x):
    print('f  running . . .%s'%x)
    time.sleep(1)
    g.switch(1)
    print('f  done . . . %s'%x)
    time.sleep(1)
    g.switch(2)

def g(x):
    print('g  running . . .%s'%x)
    time.sleep(1)
    f.switch(2)
    print('g  done . . . %s'%x)

start = time.time()
f = greenlet(f)
g = greenlet(g)
f.switch(1)
end = time.time()
print('total time : %s'%(end-start))
---------------------------------------------------------------------
f  running . . .1
g  running . . .1
f  done . . . 1
g  done . . . 1
total time : 3.0002005100250244

Process finished with exit code 0
```

- 打刚创建一个greenlet 对象的时候，首先初始化一个空的栈，当该greenlet对象调用switch方法的时候，会先运行构造greenlet对象时传入的函数（callable对象），如果这个函数中存在其他greenlet对象对switch方法的调用，那么当前这个协程就会被挂起，并保存运行状态，等再次调用其对应greenlet对象的时候切换回来。
- **当一个协程对应函数执行完毕，那么这个协程就变成dead状态**，如果一个协程被挂起后，没有再次被switch唤起，就会发生数据泄露




```python
# g2发生了数据泄露
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
----------------------------------------------
f1
g1
f2

Process finished with exit code 0
```

- 如果在线程中再次调用g.switch()，那么就会从上次被挂起的状态继续向下执行


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
g.switch()
-----------------------------------
f1
g1
f2
g2

Process finished with exit code 0

```