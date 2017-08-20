内裤可以用来遮羞，但是到了冬天它没法为我们防风御寒，聪明的人们发明了长裤，有了长裤后宝宝再也不冷了，装饰器就像我们这里说的长裤，在不影响内裤作用的前提下，给我们的身子提供了保暖的功效                                                                            ———转自知乎

## 第一类对象first-class object——python 中的一等公民
1. 在运行期创建
2. 可用作函数参数或返回值
3. 可存入变量的实体
4. 函数返回值可以是其他函数
5. 内部函数可以访问包含它的函数的局部变量

## 函数作为一等公民，都能干什么？ 
1. 把函数赋值给变量
2. 在某个函数内部定义另外一个函数


## 装饰器的本质
- 装饰器本质上是一个函数，是一个能够返回函数对象的高阶函数。


### 带有装饰器的函数的运行流程
- 代码如下，这是一个简单的装饰器函数


```python
def decorator(func):
    def wrapper(*args, **kwargs):
        print('函数执行前，要添加的功能')
        ret = func(*args, **kwargs)
        print('函数执行后，要添加的功能')
        return ret
    return wrapper

def g():
    print('被装饰函数')

g = decorator(g)
g()
```


- 具体流程

1. 内存中创建了变量decorator，指向该函数的内存空间，同时创建变量g，指向自己的函数内存空间
2. 执行g = decorator(g)，在decorator指向的函数内存空间中添加变量wrapper，它也指向一个函数内存空间。然后将变量wrapper指向的内存地址赋值给变量g。此时g不再指向原先的函数g()的内存空间。
3. 执行g()，此时g()代表的是返回的内层函数wrapper(*args,**kwargs)。解释器开始执行wrapper()函数中的代码，执行第一个print语句后。
4. 执行 ret = func(*args, **kwargs)，在wrapper内存空间中创建了变量func，指向函数g()。让后运行g()中代码，有返回值就赋值给ret，再执行下一个print语句，最后将ret作为返回值返回。


## 装饰器的作用
- 让其他函数在不需要在任何代码变动的前提下增加额外功能。
	- 有了装饰器，可以抽离出大量与函数功能本身无关的雷同代码并继续重用
## 装饰器的设计模式
- 开放封闭原则
	- 对扩展开放
		- 任何程序，不可能在设计之初就想好了所有功能，并且未来不作任何更新和修改。必须允许代码扩展，添加新功能
	- 对修改封闭
		- 已经存在的函数如果再要修改，会影响到已经使用该函数功能的用户，而且修改可能会导致程序崩溃

## 装饰器的固定格式

	def decorator(func_name):
	    def wrapper(*args, **kwargs):
	        print('执行函数前，要装饰的功能')
	        ret = func_name(*args, **kwargs)
	        print('执行函数后，要装饰的功能')
	        return ret
	    return wrapper
	
	@decorator
	def func():
	    print('要被装饰的函数')

- @decorator    <===>   func = decorator(func)  
## 有参数的装饰器
- 装饰器可以带参数，最多的用途就是作为一个装饰器的开关

```python
F = True
# F = False
import time
def outer(flag):
    def decorator(func):
        def inner(*args, **kwargs):
            if flag:
                start = time.time()
                time.sleep(2)
                ret = func(*args, **kwargs)
                print('函数运行时间====>', time.time()-start)
                # return ret
            else:
                ret = func(*args, **kwargs)
                # return ret  #重复调用语句
            return ret
        return inner
    return decorator

@outer(F)
def func1():
    print('func1 . . . .run .....')
func1()
@outer(F)
def func2():
    print('func2 . . . .run .....')
func2()
@outer(F)
def func3():
    print('func3 . . . .run .....')
func3()
@outer(F)
def func4():
    print('func4 . . . .run .....')
func4()
------------------------------------------------------------------------------------------------------------------------------
#运行结果
func1 . . . .run .....
函数运行时间====> 2.0003795623779297
func2 . . . .run .....
函数运行时间====> 2.0000627040863037
func3 . . . .run .....
函数运行时间====> 2.0010735988616943
func4 . . . .run .....
函数运行时间====> 2.0007920265197754

```

## 多个装饰器装饰一个函数
- 多个装饰器装饰一个函数，要注意装饰的功能的调用顺序



```python
def decorator_one(func):
    def inner(*args, **kwargs):
        print('decorator111111111.....decorating...before.. func')
        ret = func(*args, **kwargs)
        print('decorator111111111.....decorating...after.. func')
        return ret
    return inner

def decorator_two(func):
    def inner(*args, **kwargs):
        print('decorator222222222 .....decorating...before.. func')
        ret = func(*args, **kwargs)
        print('decorator222222222 .....decorating...after.. func')
        return ret
    return inner



@decorator_one
@decorator_two
def func():
    print(func.__name__, 'runing............')

# 等价于
# def func():
#     print(func.__name__, 'runing............')
# func = decorator_one(decorator_two(func))


-------------------------------------------------------------------------------------------------------------------------------
#输出结果
decorator111111111.....decorating...before.. func
decorator222222222 .....decorating...before.. func
inner runing............
decorator222222222 .....decorating...after.. func
decorator111111111.....decorating...after.. func
```


## 装饰器记录功能
- 用来记录装饰器装饰的函数以及执行了装饰器功能的函数

```python
import time
log_1 = []
log_2 = []
def decorator(func):
    log_1.append((func.__name__, func))    # 获取文件中所有被装饰器修饰的函数
    def inner(*args, **kwargs):
        log_2.append((func.__name__, func))         #获取被装饰并且执行过的函数
        start = time.time()
        ret = func(*args, **kwargs)
        print(time.time() - start)
        return ret
    return inner

@decorator
def f():
    print(f.__name__, 'f  run ....')
f()

@decorator
def g():
    print(g.__name__, 'g run ....')

print(log_1)
print(log_2)
---------------------------------------------------------------------------------------------------------------------
#运行结果
inner f  run ....
0.0
[('f', <function f at 0x000002ED8FE1B8C8>), ('g', <function g at 0x000002ED8FE1B9D8>)]
[('f', <function f at 0x000002ED8FE1B8C8>)]
```



## 合并多个装饰器的方法
- 先看代码

```python
def decorator_one(func):
    def inner(*args, **kwargs):
        print('decorator111111111.....decorating...before.. func')
        ret = func(*args, **kwargs)
        print('decorator111111111.....decorating...after.. func')
        return ret
    return inner

def decorator_two(func):
    def inner(*args, **kwargs):
        print('decorator222222222 .....decorating...before.. func')
        ret = func(*args, **kwargs)
        print('decorator222222222 .....decorating...after.. func')
        return ret
    return inner
# 正常使用是这样的
#
# @decorator_one
# @decorator_two				# 等价于    func = decorator_one(decorator_two(func)) 
# def func():
#     print(func.__name__, 'runing............')
#
# func()

def composed(*decs):
    def deco(f):
        for dec in reversed(decs):   # 注意顺序
            f  = dec(f)
        return f
    return deco
#现在
@composed(decorator_one, decorator_two)
def func():
    print(func.__name__, 'runing...........')
func()

```

- 这是因为     


```python
# @decorator_one
# @decorator_two	#  =====>>>等价于     func = decorator_one(decorator_two(func))
# def func():
	'''balabala....'''
	pass
```

> 参考：https://stackoverflow.com/questions/5409450/can-i-combine-two-decorators-into-a-single-one-in-python



##装饰器练习
1. 编写装饰器，实现缓存网页内容的功能：
具体：实现下载的页面存放于文件中，如果文件内有值（文件大小不为0），就优先从文件中读取网页内容，否则，就去下载，然后存到文件中

```python
url_l = []
from urllib.request import urlopen

def get_cache(func):
    def inner(*args,**kwargs):
        url = args[0]
        filename = str(hash(url))
        if url in url_l:
            f = open(filename,'rb')
            ret = f.read()
        else:
            url_l.append(url)
            ret = func(*args, **kwargs)
            f = open(filename,'wb')
            f.write(ret)
        f.close()
        return ret
    return inner

@get_cache
def get(url):
    return urlopen(url).read()
```


2. 编写装饰器，为多个函数加上认证的功能（用户的账号密码来源于文件），要求登录成功一次，后续的函数都无需再输入用户名和密码。 注意：从文件中读出字符串形式的字典，可以用eval('{"name":"egon","password":"123"}')转成字典格式


```python
'''
编写装饰器，为多个函数加上认证的功能（用户的账号密码来源于文件），
要求登录成功一次，后续的函数都无需再输入用户名和密码
注意：从文件中读出字符串形式的字典，
可以用eval('{"name":"egon","password":"123"}')转成字典格式
'''

user_dic = {
    'user' : None,
    'status' : False
}
def get_dic(file):
    with open(file) as f:
        data = f.read()
    return data
def check_info(func):
    def inner(*args, **kwargs):
        if not user_dic['status']:
            name = input('name => ').strip()
            password = input('password => ').strip()
            # user_info = eval(open('info.txt', 'r', encoding='utf-8').read())
            user_info = eval(get_dic('info.txt'))
            if user_info.get(name) and user_info[name] == password:
                print('login successful')
                user_dic['user'] = name
                user_dic['status']=True
                ret = func(*args, **kwargs)
                return ret
            else:
                print('login failed')
        else:
            ret = func(*args, **kwargs)
            return ret
    return inner


@check_info
def f1():
    print(f1.__name__, 'f1 runing......')
f1()
@check_info
def f2():
    print(f2.__name__, 'f2 runing......')
f2()
@check_info
def f3():
    print(f3.__name__, 'f3 runing......')
f3()
@check_info
def f4():
    print(f4.__name__, 'f4 runing......')
f4()

```


## 函数注释被装饰器装饰的时候，func.__doc__显示函数注释的方法
- functools.wraps装饰器

```python
# 当函数被装饰器装饰ide时候，是无法调用出其文档字符串的
def outer(func):
    def inner(*args,**kwargs):
        return func(*args, **kwargs)
    return inner
@outer
def func():
    '''这是一个函数文档字符串，当被装饰器装饰的时候,func.__doc__不会调用出其注释，可以是使用func.wraps装饰器解决这个问题

    :return: None
    '''
    pass

print(func.__doc__)
--------------------------------------------------------------------------
# 结果
None


---------------------------------------------------------------------------------
# 解决方法

import functools

def outer(func):
    @functools.wraps(func)
    def inner(*args,**kwargs):
        return func(*args, **kwargs)
    return inner
@outer
def func():
    '''这是一个函数文档字符串，当被装饰器装饰的时候,func.__doc__不会调用出其注释，可以是使用func.wraps装饰器解决这个问题

    :return: None
    '''
    pass

print(func.__doc__)


-------------------------------------------------------------------------------------------------
# 结果
D:\Python36\python.exe D:/files/python_practice/20170814-0820/0814/practice.py
这是一个函数文档字符串，当被装饰器装饰的时候,func.__doc__不会调用出其注释，可以是使用func.wraps装饰器解决这个问题

    :return: None
```

## 装饰一个类

```python
import time

def wrapper(aclass):
    def inner(*args, **kwargs):
        print('%s 开始运行'%aclass.__name__)
        start = time.time()
        obj = aclass(*args, **kwargs)
        print('%s 运行了%s S'%(aclass.__name__, time.time()-start))
        return obj
    return inner

@wrapper
class DayAction:
    def __init__(self):
        time.sleep(2)

d = DayAction()

-------------------------------------------------------------------------------------------
# 运行结果
DayAction 开始运行
DayAction 运行了2.0000367164611816 S
```

