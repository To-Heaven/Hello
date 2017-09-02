# timeit 模块
> contrary     相反的   

## Note
- while using the interface of command line,  parameter of setup is on  the left side os parameter of statement, but on the contrary while using the interface of python script


## Interface for command line 
- `python -m timeit [-n num] [-r num] [-s setup] [statement]`
	- `-n`  how many times to execute ‘statement’
	- `-r`   how many times to repeat the timer (**default 3**)
	- `-s`   statement to be executed once initially (default pass)
	- `statement`  statement


### module-level functions
- timeit.timeit(statement='passs', setup='pass', timer=<default timer>, number=1000000, globals=None)
	-  returns the time it takes to execute the main statement a number of times, measured in seconds as a float
	- Create a Timer instance with the given statement, setup code and timer function and run its timeit() method with number executions
	- **default timer** is always time.perf_counter()
	- **globals**: specifies a namespace in which to execute the code.

- timeit.repeat(stmt=’pass’, setup=’pass’, timer=<default timer>, repeat=3, number=1000000, globals=None)
	- returns the time it takes to execute the main statement a number of times, measured in seconds as a float
	- Create a Timer instance with the given statement, setup code and timer function and run its repeat() method with the given repeat count and number executions
	- **repeat** :specifies how many times to call timeit()


## Timer class
- timeit.Timer(statement='passs', setup='pass', timer=<default timer>, globals=None)
	-  return a Timer obejct which is used for timing exection
	-  if globals is None:
		-  __The statement will by default be executed within timeit’s namespace__

## Timer object Methods
- t.timeit(number=1000000)
	-  Time number executions of the main statement
	-  returns the time it takes to execute the main statement a number of times, measured in seconds as a float
	-  **timeit() temporarily turns off garbage collection during the timing**

- t.autorange(callback=None)
	- return a tuple of  (times, spend_time)
		-  It calls timeit() with number set to successive powers of ten (10, 100, 1000, …) up to a maximum of one billion, until the time taken is at least 0.2 second, or the maximum is reached.
		-  会对statement测试10的整数倍的次数，直到测试花费的时间大于0.2s为止

```python
import timeit

statement = '''\
for i in range(n):
    s += i
'''

setup = '''\
n = 100000; 
s = 0
'''


t = timeit.Timer(statement, setup)
print(t.timeit(1000))
print(t.autorange())
----------------------------------------------------------
15.094152293821587
(100, 1.5104606954485646)

Process finished with exit code 0

```



- t.repeat(repeat=3, number=1000000)
	- repeat call timeit() a few times
	- returning a list of results
	- *parameters*:
		- repeat:  specifies how many times to call timeit()
		- number: specifies the number argument for timeit().

```python
import timeit

statement = '''\
for i in range(n):
    s += i
'''

setup = '''\
n = 100000; 
s = 0
'''


t = timeit.Timer(statement, setup)
print(t.timeit(100))
print(t.repeat(3, 100))
-----------------------------------------------
1.6505624419151992
[1.6527072043257336, 1.5690952651080825, 1.6293193970043331]

Process finished with exit code 0
```

- t.print_exc(file=None)
	- print a traceback from the timed code if an excection araised
	- file: default to sys.stderr 

```python
import timeit

statement = '''\
for i in range(n):
    s += i
'''

setup = '''\
n = 100000; 
s = 0
'''


t = timeit.Timer(statement, setup)

try:
    t.timeit(100)
    raise Exception
except Exception:
    t.print_exc(open('traceback.txt', 'w'))
------------------------------------------------------------------------
# traceback.txt
Traceback (most recent call last):
  File "D:/files/python_code/demo.py", line 48, in <module>
    raise Exception
Exception
```

## examples


```python
def test():
    l = [i for i in range(100)]

if __name__ == '__main__':
    import timeit
    print(timeit.timeit('test()', 'from __main__ import test'))
--------------------------------------------------------------------------------------

7.951334894330924

Process finished with exit code 0

```

- 关于globals参数

```python
import timeit


def f(x):
    return x**2
def g(x):
    return x**4
def h(x):
    return x**8

print(timeit.timeit('[func(42) for func in (f,g,h)]', globals=globals()))
print(timeit.timeit('[func(42) for func in (f,g,h)]'))
---------------------------------------------------------------------------------------
3.5928753334218433				# 加入参数globals()，可以使用全局命名空间变量
Traceback (most recent call last):		# 不加入，默认使用timeit模块的命名空间，报错
  File "D:/files/python_code/demo.py", line 155, in <module>
    print(timeit.timeit('[func(42) for func in (f,g,h)]'))
  File "D:\Python36\lib\timeit.py", line 233, in timeit
    return Timer(stmt, setup, timer, globals).timeit(number)
  File "D:\Python36\lib\timeit.py", line 178, in timeit
    timing = self.inner(it, self.timer)
  File "<timeit-src>", line 6, in inner
NameError: name 'f' is not defined

Process finished with exit code 1
```