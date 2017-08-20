## 定义函数
- 格式

```python
def func(参数1，参数2 ...):
	'''函数的注释'''
	print('函数体')
	return '返回值'
```

- 注意：
	- def关键字，其后必须跟有函数名称和形式参数的圆括号。
	- 函数体
		- 从def关键字所在语句的下一行开始，并且必须要有缩进
		- 函数体第一行语句可以是可选的字符串文本（docstring，文档字符串）。
	- 在函数名下一行对函数进行文档注释'''......'''
	- **函数要先定义，之后才能调用**，因为python解释器时从上向下解释python脚本的    
	- **每一次创建函数的时候，都应该有文档字符串对函数进行说明**
	- **函数名其实就是对一个函数对象的引用**，完全可以把函数名赋给一个变量，相当于给这个函数起了一个‘别名’，可以把函数名再复制给另一个变量，通过该变量来调用这个函数。
		- **同一个函数在内存中只有一份，也就是说即使有了另一个名称，也是指向相同的内存地址**
		- 函数名必须由字母下划线数字组成，不能是关键字，不能以数字开头
		- 函数名要有一定意义，能够说明函数的功能

```python
>>> a = [1,2,3,4,5,6]
>>> max(*a)
6
>>> m = max
>>> m(*a)
6
>>> 
```

## python函数在内存中的运行流程（以func为例子）

```python
def func(name):
	return name

a = func('ziawang')
```
1. python解释器从上到下开始解释脚本中的代码，当读到def关键字的时候，将函数以及函数体中的代码加载到内存。
2. python解释器运行func('ziawang')，将实参传入到函数，执行函数体中的代码，最后返回值通过等号赋值给变量a

## 函数的返回值

- 函数的返回值可以是任意类型，如果有返回值，必须要有变量接收才会有效，当有多个返回值时，用多个变量接收，但是变量的数量要和等式右边元组元素数量相同
- Python的函数**返回多个值其实就是返回一个tuple**
	- 当用一个变量接收多个返回值的时候，实际上是先创建了一个元组，然后再返回的。变量就指向一个元组，如果用两个变量就相当于序列拆包
- 函数的返回值可以是一个变量，这个变量指向一个对象
- 函数体内部的语句在执行的时候，一旦遇到了return，函数就会执行完毕，并将结果返回。
- 函数体内可以不写返回值，如果函数定义时候没有写return，函数执行完毕也会返回结果，只是结果为None
	- return None 可以简写为  return

```python
def outer():
    def inner():
        return 'haha'
    inner()

outer()    #  这里的outer()是没由返回值的，
# 要想在调用outer()获取inner返回值。
def outer():
    def inner():
        return 'haha'
    return inner
#这样就可以了
```


## pass
- 函数定义过程中，如果某个语块还没想好该怎么写函数的代码，就可以先放一个pass，让代码能运行起来

```python
def f():
    s = input('请输入你想说的话： ').strip()
    if len(s) == 0:
        pass
    else :
        print(s)
```

- 函数定义时，函数体内应有return，即使return返回None，因为return还是有一个作用：**当函数运行到return的时候，函数就执行完毕，可以利用这一特性与条件语句结合**，代码如下：

```pyhton
def func(a, b):
	''''''
    	if type(a)==int:
        		return a
   	 else:
       		 return a, b

s = func(10, 'haha')
print(s)
```
```python
def func(l):
    if type(l) in (list, str, tuple):
        return l[len(l)-1]                 # 直接  return l[-1]更好，不需要额外的len()调用
    else:
        pass

s = func([1, 2, 3, 4])
print(s)

s = func('haha')
print(s)

s = func((1,2,3,4,5,6))
print(s)
```

- return返回值在循环遍历中存在于条件判断时注意

```python
#创建一个用来检测判断字符串，列表或元组中是由有空元素的函数。

def check_out(obj):
    if obj:
        if type(obj) is str:
            for i in obj:
                if not i:
                    return True
                #这里不能  else:  return False ，会导致函数再第一个元素就运行结束 
        elif type(obj) in (list, tuple):
            for i in obj:
                if not i:
                    return True 
                #同上，这里也不能  else :return False ，会导致遍历第一个元素就结束函数
    else:
        return True
  
```

## 了解命名空间之前

```python
>>> def my_max(x, y):
	m = x if x>y else y

	
>>> my_max(10, 20)
>>> print(m)
Traceback (most recent call last):
  File "<pyshell#17>", line 1, in <module>
    print(m)
NameError: name 'm' is not defined
>>> 
```

- **为什么报错？**
	1.  python解释器运行文件之后，在内存中开辟了一个内存空间
	2. 每次遇到一个变量，解释器就会把变量名和变量值之间的关系记录下来。
	3. 当解释器读到def关键字的时候，只是将函数名读入内存，表示指导这个函数存在了，对于函数内部的变量和逻辑，解释器不关心
	4. 当调用函数的时候，解释器再开辟一块内存来存储这个函数里面的代码，此时函数中的变量会存储再新开辟出来的内存中。**这个函数中的变量只能在函数的内部使用**
	5. 函数执行完毕，内存中这个函数的内存空间被清空，包括这个函数中定义的一些变量也会被清空（因为这些变量就是定义在该函数的内存空间中的）

- 而上面第2步，存放名字和值的关系的空间就叫做命名空间
	- 用来存储该函数中的‘变量名和值的关系’的空间就叫做局部命名空间
	- 在代码运行开始的时候，创建的存储‘变量名和值’的内存空间就是全局命名空间


## 函数调用中常见的错误
- TypeError
	- 参数类型错误
	- 参数数量错误


## 命名空间
- **函数的命名空间随着函数调用开始而创建，随着函数调用结束而释放**
- 文件调用时，命名空间的加载顺序（从硬盘上加载到内存中）
	- 内置命名空间-----> 全局命名空间-----> 局部命名空间
- 局部命名空间(函数)
	- 在函数运行中开辟的临时空间
- 全局命名空间（整个文件）
	- 代码运行开始的时候，创建的存储“变量名与值的关系”的空间
- 内置命名空间


## 函数名的本质 ----一个变量，指向存放函数代码的内存地址
- 函数名不加括号——函数的内存地址
- 函数名加括号——调用函数
- 函数名的相关使用（当作普通变量使用）
	- 直接被引用
	- 当作容器类型数据的元素
	- 当作其他函数的参数或者返回值
- **同一个函数在内存中只有一份，也就是说即使有了另一个名称，也是指向相同的内存地址**

```python
>>> def func():
	print(' func')

	
>>> print(func)
<function func at 0x0000028BD037F488>
>>> a = func
>>> a
<function func at 0x0000028BD037F488>
>>> l = [func]
>>> l
[<function func at 0x0000028BD037F488>]
>>> l[0]()
 func
>>> 
```
 
## 函数名作为参数传入
- 函数名本身是一个变量，指向了存放该函数代码的内存空间


```python
def func():
    print('func')

def gunc(f):
    print(f)
    print('gunc')

gunc(func)
-------------------------------
<function func at 0x0000025C53403E18>
gunc

```

## 函数名作为函数的返回值

```python
def func():
    print('func')

def gunc(f):
    print('gunc')
    return f

s = gunc(func)
print(func)
print(s)                                     #内存地址相同
s()
-------------------------------
gunc
<function func at 0x0000019CB1053E18>
<function func at 0x0000019CB1053E18>
func
```

- 在全局命名空间中利用返回值调用局部命名空间中的函数

```python
def func():
    def f():
        print('hello ')
    return f
a = func()
a()
------------------------------
hello

``` 
```python
def f(iterable, func):
    l = []
    for i in iterable:
        l.append(func(i))
    return l

l = f([1,2,3], lambda i : i*3)
print(l)

```




## 作用域(只有两种)
- 为什么要有作用域？
	- 为了函数内的变量不影响到全局
- 全局作用域：全局命名空间， 内置命名空间
- 局部作用域：局部命名空间
- 命名空间中变量的调用
	- 在局部命名空间中可以使用内置命名空间和全局命名空间的数据，但是后两者却不能使用局部命名空间的数据，同理，全局命名空间中可以使用内置内名空间的数据，但是内置命名空间中却不能使用全局命名空间的数据。
	- 简单来说，就是小范围不能使用大范围的，大范围可是使用小范围的
		- 范围从大到小： 内置命名空间   >  全局命名空间   >   外部函数局部命名空间   > 内层函数局部命名空间
		- 如果在小范围内没有，就用上一级的，上一级没有就用上上一级，以此类推，如果都没有，报错
	- **但是从外部仍然可以间接访问内部变量的，可以将想要访问的内部变量的变量名返回**

```python 
def outer():
	b = 10
	def inner():
		print(b)
	inner()

outer()

-------------------------------------
 10
```


- 函数的嵌套调用
	- 在函数加载进内存的时候，不同的函数f1和f2分别创建了自己的内存空间，两者的变量不能相互访问
	- **作用**：

```python
def f1():
	print('f1')

def f2():
	a = 10              #变量a不能被f1()调用
	f1()

-----------------------------
def animal():
	def tiger():
		print('tiger   run....')
	print('eat')

tiger()        # 在全局命名空间中直接调用animal局部命名空间中的tiger()是会报错的
-----------------
#运行结果
>>> tiger()
Traceback (most recent call last):
  File "<pyshell#2>", line 1, in <module>
    tiger()
NameError: name 'tiger' is not defined


```

- 作用域链

```python
x = 0
def f1():
    x = 1		#注释了x=1,x=2输出0
    def f2():
        x = 2     		#注释了x=2,输出1
        def f3():
            print(x)
        f3()
    f2()


f1()

def f1():
	a = 1
	def f2():
		a = 2
	f2()
	print('a in f1 : ', a)

f1()
```

## globals()和locals()
- globals()函数会返回当前文件中的全局变量。
- locals()会返回**当前所在命名空间**的局部变量，如果在全局命名空间中调用locals()函数，返回值和globals()的返回的值相同

```python
a = '这是一个全局变量'
def f():
    a = '这是一个局部变量'
    b = '这是另一个局部变量'
    print('局部命名空间中的globals()', globals())
    print('局部命名空间中的 locals()', locals())
f()
print('全局命名空间中的globals()', globals())
print('全局命名空间中的  locals()', globals())
```

输出结果：


```python
局部命名空间中的globals() {'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x0000019BF000A320>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, '__file__': 'D:/files/python_practice/20170724--0730/0726/onclass.py', '__cached__': None, 'a': '这是一个全局变量', 'f': <function f at 0x0000019BEFF93E18>}
局部命名空间中的 locals() {'b': '这是另一个局部变量', 'a': '这是一个局部变量'}
全局命名空间中的globals() {'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x0000019BF000A320>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, '__file__': 'D:/files/python_practice/20170724--0730/0726/onclass.py', '__cached__': None, 'a': '这是一个全局变量', 'f': <function f at 0x0000019BEFF93E18>}
全局命名空间中的  locals() {'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x0000019BF000A320>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, '__file__': 'D:/files/python_practice/20170724--0730/0726/onclass.py', '__cached__': None, 'a': '这是一个全局变量', 'f': <function f at 0x0000019BEFF93E18>}
```

## global 和nonlocal（python2中没有nonlocal，目的是为了替代global）
- golbal：如果要在局部变量中修改全局变量，就需要在局部命名空间中对该全局变量进行声明。如果只是调用全局变量的话就不用声明，因为python中，局部命名空间内可以使用全局变量空间和内置命名空间的变量。

```python

a = 1
b = '不修改，只调用的话可以不用global声明'
def func():
    global a
    a = 10
    print(a)
    print(b)
func()

```

- nonlocal 
	- 使用条件：
		1. 只能作用于内层函数的上一层函数，外部函数必须有nonlocal声明的变量，如果内部函数
		2. 内部函数声明nonlocal之前，不能再出现同名变量
		3. nonlocal修改的是最近一层的作用空间中的变量，如果在上一层没有该变量，就会去上上一层去寻找，以此类推，如果都没有找到，就会不满足条件1，最终报错
	- 作用：可以在内层函数中去更改上一层函数的变量（与global不同，global声明的是全部命名空间的变量）

```python
 def f1():
    x = 1
    def f2():
        x = 2
        def f3():
            nonlocal x
            x = '只能修改上一层函数中存在的变量'
        print('未nonlocal之前 , x = ', x)
        f3()
        print('nonlocal之后， x = ', x)
    f2()

f1()
```

输出结果

```python
未nonlocal之前 , x =  2
nonlocal之后， x =  只能修改上一层函数中存在的变量

```


## 闭包（wrapper）
- 闭包是什么鬼？
	- 闭：内部的函数
	- 包：外部的变量
	- 闭包一个函数，但是这个函数必须是一个内部函数（函数内部定义的函数），而且包含了对外部作用域中变量(**这个变量不包括全局变量**)的引用
	- 维基百科中，这样解释：

	在计算机科学中，闭包（Closure）是词法闭包（Lexical Closure）的简称，是引用了自由变量的函数。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。所以，有另一种说法认为闭包是由函数和与其相关的引用环境组合而成的实体。闭包在运行时可以有多个实例，不同的引用环境和相同的函数组合可以产生不同的实例。
	
## 闭包的三个条件
1. 需要函数嵌套，在一个函数中定义另一个函数
2. 外部函数需要返回一个内部函数的引用
3. 外部函数中有一些局部变量或者参数，并且这些局部变量或参数在内部函数中有使用
 

- 看个栗子

```python
def func(name):
    def inner_func(age):
        print('name: ', name, '  age: ',age)
    return inner_func

bb = func('ziawang')
bb(23)
---------------------------------------
name:  ziawang   age:  23
```

- 调用func()的时候返回的就是inner_func，自由变量就是name
- 当函数func的生命周期结束之后，name这个变量依然存在，因为他被闭包引用了，所以不会被回收
- 将闭包函数作为外层函数的返回值返回给另一个对象，可以防止臂包中的函数inner()被释放
	- 内层函数调用外层函数变量，保留住自由变量
	- 将内层函数作为返回值，保留住闭包中的函数
- 如何判断一个函数是不是闭包函数以及获取自由变量的值
- inner.__closure__   返回一个存放自由变量内存地址的元组， 内部函数非闭包会返回None。
	- __closure__[i].cell_contents       返回元组中索引为i的自由变量的值
	- 判断语句应该放在与被测函数相同的命名空间中。


```python
def outer():
    name = 'ziawang'
    def inner():
        print('这是一个闭包函数', name)
    print('判断是否为闭包函数', inner.__closure__)
    print('获取自由变量', inner.__closure__[0].cell_contents)

outer()
--------------------------
#输出结果：
判断是否为闭包函数 (<cell at 0x000001FC352D65B8: str object at 0x000001FC35369458>,)
获取自由变量 ziawang

```
```python
def outer():
    def inner():
        name = 'ziawang'
        print('这是一个闭包函数', name)
    print('判断是否为闭包函数', inner.__closure__)
    inner()

outer()
--------------------------------------------------------------
#运行结果
判断是否为闭包函数 None
这是一个闭包函数 ziawang

```




- 闭包的作用：
	- 在外层函数运行完毕释放的时候，保留住外层函数中的变量（这个变量就是闭包中的自由变量）
	- 就是能够应用外部函数的变量，并且还能保证外部的外部的变量被控制在一个局部作用域当中。这样可以保护变量（外部命名空间就不能对自由变量进行更改）
	- 先举个例子

```python
def outer1(i):
    x = 11111111111111
    if type(i) is int:
        return x**i

def outer2():
    x = 11111111111111
    def inner(i):
        if type(i) is int:
            return x**i
    return inner
```
- 现在有一个需求，要把变量x的值进行运算，如果用函数outer1()来完成，那么每次outer1()运行一遍，内存中都会进行一次变量x的创建和释放，在大规模爬虫中，这样重复对x进行同样处理会大大降低性能。使用了
- 之后，将闭包的内层函数作为返回值，这样就避免了在函数outer2()运行解释时释放x，不用重复创建，因为只需要创建一次。
- 闭包的应用（常用形式）

- 应用一

```python
from urllib.request import urlopen
def outer(url):
    def inner():
        return urlopen(url).read()
    return inner

get = outer('https://github.com/ZiaWang')
data = get()
with open('ziawanggithub.txt', 'w') as z:
    print(data, file=z)

```

- 应用二 装饰器  


```python

def decorator_func(function):
    def wrapper(*args, **kwargs):
        return function(*args, **kwargs)
    return wrapper


@decorator_func
def func(name):
    print('my name is', name)

```



## 不可变数据类型和可变数据类型在变量作用域中的区别！！！！！！
- 如果变量指向的时不可变数据类型，在内层命名空间修改外层命名空间只能使用global和nonlocal关键字
- 如果变量指向的时字典、列表这类可变的数据，在内层命名空间中就可以直接修改。

```python

# 编写装饰器，为多个函数加上认证的功能（用户的账号密码来源于文件），
# 要求登录成功一次，后续的函数都无需再输入用户名和密码
# 注意：从文件中读出字符串形式的字典，
# 可以用eval('{"name":"egon","password":"123"}')转成字典格式
# tag = True
# def check_info(func):
#     def inner():
#         global tag
#         if tag:
#             name = input('请输入姓名： ').strip()
#             password = input('请输入密码：').strip()
#             if name == get_info('a')['name'] and password == get_info('a')['password']:
#                 tag = False
#                 ret = func()
#                 return ret
#             else:
#                 ret = func()
#                 return ret
#         else:
#             ret = func()
#             return ret
#     return inner


# dic = {}
# def check_info(func):
#     def inner():
#         if not dic:
#             name = input('请输入姓名： ').strip()
#             password = input('请输入密码：').strip()
#             if name == get_info('a')['name'] and password == get_info('a')['password']:
#                 dic[name] = password
#                 ret = func()
#                 return ret
#         else:
#             ret = func()
#             return ret
#     return inner


@check_info # f1 = wr(f1)
def f1():
    print('f1   run ...')
f1()

@check_info
def f2():
    print('f2   run ...')
f2()

@check_info
def f3():
    print('f3   run ...')
f3()

@check_info
def f4():
    print('f4   run ...')
f4()

```



## 函数中的变量

- 局部变量
	- 当在**函数定义内声明变量**（函数体内仍可以调用函数外的变量，但是不能改变外部变量的值）时候，他们与函数外具有相同名称的其他变量没有任何关系。
- 函数调用会为函数体中的局部变量生成一个新的符号表（symbol table），函数中局部变量的值都是存储在局部符号表中
-  引用变量时
	1. 解释器首先在局部符号表中查找变量
	2. 然后是包含有该函数的局部符号表中查找
	3. 接着再在全局符号表中寻找
	4. 最后是内置名字表

> **全局变量不能在函数中直接赋值**，尽管可以被引用，除非用global语句命名。

```python
# test.py
def fun(x):
    print('x is',x)
    x = 2
    print('Changed local x to ',x)
    
x = 100
fun(x)
print('x is still ',x)
```
- 运行结果

```python
>>> 
================= RESTART: C:/Users/Zia Wang/Desktop/test.py =================
x is 100
Changed local x to  2
x is still  100
>>> 
```

	1. 函数fun定义范围之外的变量与函数内部的变量没有任何关系，即使变量名相同。
	2. 当将函数外的变量值x作为函数的实参传入函数的时候，只能说将函数fun局部符号表中的变量x指向了实参100，然后打印输出print('x is ', x)
	3. 接着，函数fun内部进行了再次赋值，函数局部符号表的x（局部变量x）不再指向100，而是指向了2，这是函数内部的x
	4. 函数调用完毕，内存空间释放，函数内部的变量x也随之释放掉。
	5. 全局变量x（函数fun之外的变量x）在全局符号表中仍然存在，因为程序还没有运行完毕，接着打印输出全局变量中的x，该x值从始至终一直都是指向的100

- **函数引用的实际参数在函数调用时引入局部符号表，所以实参总是传值调用，这里的值总是被一个对象调用，而不是该对象的值。当一个函数被另一个函数调用的时候，一个新的局部符号表在调用过程中被创建(意思就是两个函数的变量之间是不在一个局部符号表上的)，代表了被调用函数自身局部变量的作用域**
	- 实际参数：调用函数时传入的参数值，参数列表中实实在在存在的数据（实参也可以是一个指向一个存在着的数据的变量）
	- 形式参数：定义函数时定义的参数名，它用来代表函数调用时的实参的变量

```python
def fun(x):
    x = 2
    print('Changed local x to ',x)


def g(x):
    print('x is',x)
    fun(x)
    
x = 100
g(x)
print('x is still ',x)
```

- 运行结果

```python
>>> 
================= RESTART: C:\Users\Zia Wang\Desktop\test.py =================
x is 100
Changed local x to  2
x is still  100
>>> 

```
- 没有返回值return的函数也会有一个返回值，就是None，可以被print打印输出

```python
>>> def f():
	pass

>>> print(f())
None
>>> 
```

## 参数定义顺序
- 位置参数---->*args----->默认参数------>**kwargs
	- 传参数的时候，必须先按照位置传参数，再按照关键字传参数




# 参数传递的方式只有两种
1. 按照位置传参数。   *()或*[]可传多个位置参数
2. 关键字形式传参数，但是关键字参数要放在位置参数后面(如果混用的话)，**{}可传多个关键字参数

## 位置参数
- 调用函数的时候位置参数可以和关键字参数混用。关键字参数传入参数时可以不用遵守位置的对应关系，如果关键字参数和位置参数混用的话，关键字参数必须写在位置参数后面

```python
def func(a, b, c):
	print(a , b , c)

-----------------------
# 位置参数调用
func(1,  2, 3)
# 关键字参数调用
func(3, a = 1, b = 2)
```


## 默认参数值
- **默认参数必须指向一个不变的对象**
	- Python函数在定义的时候，默认函数的值就被计算出来了，默认参数也是一个变量，它指向一个对象，每次调用该函数，如果改变了变量的内容，下次调用的时候，默认参数的内容就改变了，不再是函数定义时候的值了。

```python
def f(hobby = 'haha'):
    print(hobby)
    hobby = hobby.replace('haha', 'xixi')
    return hobby


def g(hobby = []):
    print(hobby)
    hobby.append('xixi')
    return hobby

```

>  交互式界面运行结果

```python
>>> g()
[]
[ 'xixi']
>>> g()
[ 'xixi']
['xixi', 'xixi']
>>> g()
[ 'xixi', 'xixi']
[ 'xixi', 'xixi', 'xixi']
>>> f()

'xixi'
>>> f()
haha
'xixi'
>>> f()
haha
'xixi'
>>> 
```


```python
def func( l = []):
	l.append('haha')
print(func([]))                           #这种调用就不会出错，因为每次调用函数的时候，都将l的值默认初始化成了[]，如果不指定的话，就会遇到和上面相同的结果，即l的值被改变
print(func([]))
print(func([]))
print(func([]))
print(func([]))
print(func([]))
print(func([]))
```




可以间接的传入一个可变参数


```python
def g(hobby=None):
    if hobby==None:
        hobby=[]
    hobby.append('xixi')
    return hobby
print(g())
print(g())
print(g())
```


- 默认参数必须在必选参数（位置参数）的后面，否则解释器会报错
- 当函数有多个默认参数的时候，将变化大的参数放在前面，变化小的参数放后面。
- 降低调用函数的难度，把常用的固定参数值设置成默认参数可以方便函数调用，降低参数调用的复用性
- **函数调用时，如果不想按照参数顺序出入参数或者默认参数值只自己初始化一部分的时候，就需要曹勇关键字形式来传参**


```python
def info(name, age, city='Bozhou', language='chinese'):
    d = {'name':name, 'age':age, 'city':city, 'language':language}
    return d
#完整传参
s = info('ziawang', 23, 'Beijing', 'Chinese, English')
print(s)
#部分传参，不按顺序,程序会按照位置顺序传入参数，导致参数匹配错误，这种情况需要用关键字来传参
s = info('ziawang', 23,'Chinese, English')
print(s)
#部分传参，按照顺序，输出结果正确
s = info('ziawang', 23, 'Beijing')
print(s)
#使用关键字传参
s = info('ziawang', 23, language='Chinese, English')
print(s)
#完整传参，关键字形式
s = info('ziawang', 23, language='Chinese, English', city='Beijing')
print(s)
----------------------------------------------------------------------------------------------------
{'name': 'ziawang', 'age': 23, 'city': 'Beijing', 'language': 'Chinese, English'}
{'name': 'ziawang', 'age': 23, 'city': 'Chinese, English', 'language': 'chinese'}
{'name': 'ziawang', 'age': 23, 'city': 'Beijing', 'language': 'chinese'}
{'name': 'ziawang', 'age': 23, 'city': 'Bozhou', 'language': 'Chinese, English'}
{'name': 'ziawang', 'age': 23, 'city': 'Beijing', 'language': 'Chinese, English'}


```
```python
def ask_ok1(prompt, retries=4, complaint='Yes or no, please!'):
    '''
    定义一个函数，询问用户是否退出，
    设定用户错误输入次数
    '''
    while retries>0:
        ok = input(prompt)
        if ok in ('y', 'ye', 'yes'):
            print('OK,quit now!')
            break
        elif ('n' or 'no') in str :
            print('OK,do not quit!')
            break
        else :
            print(complaint)
            retries = retries - 1

        
def ask_ok2(prompt, retries=4, complaint='Yes or no, please!'):
    '''
    有返回值的这个函数在进行程序设计的时候可以利用返回值对流程进行控制，
    而上面的函数无返回值
    '''
    while True:
        ok = input(prompt)
        if ok in ('y', 'ye','yes'):
            return True
        elif ok in ('n', 'no'):
            return False
        retries = retries - 1
        if retries < 0:
            raise OSError('uncooperative user')
        print(complaint)

def shutdown_computer():
    if ask_ok2('shutdown now? '):
        print('shutdowning......')
    else :
        print('continue working.......')

shutdown_computer()        
'''
```

```python
def login_successful():
    print('login successful ') if login else print('login failed ')

def user_login(user, password):
    return True if user=='ziawang' and password=='123' else False

login = user_login('ziawang', '123')
login_successful()


```

> 看运行结果


```python
================= RESTART: C:\Users\Zia Wang\Desktop\haha.py =================
shutdown now? n
continue working.......
>>> shutdown_computer()
shutdown now? y
shutdowning......
>>> 
```

- **默认值在函数定义作用域被解析**

```python
i= 5

def fun(arg=i):
    print(arg)

i = 6
fun()
```

> 运行结果

```python
5
```

> 可以将上述代码更简单的看成

```python
def fun(arg=5):
	print(arg)
i = 6
fun()
```

> 可以按照需求对ask_ok2函数进行个性化自定义

- **默认值只被赋值一次**
	- 当默认值是可变对象的时候（比如列表）会有不同。

```python
def fun1(i, l=[]):
    '''
    当默认参数是可变的类型时候，在函数的不断运行时，
    该参数的值会变化！！
    '''
    l.append(i)
    return l

print(fun1(1))
print(fun1(2))
print(fun1(3))

def fun2(i, l=None):
    '''将l 变成不可变对象'''
    if l is None:
        l = []
    l.append(i)
    return l
print(fun2(1))
print(fun2(2))
print(fun2(3))

```

> 运行结果

```python
[1]
[1, 2]
[1, 2, 3]
[1]
[2]
[3]
```

## 可变参数     （参数列表中，可变参数必须在关键字参数之前）
- 定义方式	def    func(*args) 
- 可变参数允许传入0个或任意个参数，这些可变参数在函数调用时自动组装成一个tuple。
- 可变参数所在位置通常是参数列表的最后一个，因为他们把所有的剩余输入参数传递给函数。
- 任何出现在可变参数*args后的参数是关键字参数
- *args表示把args这个序列的所有元素作为可变参数传入进函数，按位置传参
- args是由所有其他位置参数组成的元组，在代码中可以把args当作一个序列来调用

```python
def  fun(*args):
    sum = 0
    for i in args :
        sum = sum + i
    return sum


l = [1, 2, 3, 4, 5, 6, 7]
print(fun(*l))       # 记得加上 *  ，不然解释器无法识别是否是可变参数
```

## 关键字参数
-  参数列表中，定义方式 key = value
- 关键字参数允许传入0或任意个含有参数名的参数，这些关键字参数在函数内部自动组装成一个字典dict
- 关键字参数用于扩展函数的功能
- 类似可变参数，可以**将一个字典dict转换陈关键字参数传进去**，按关键字传参
- kwargs是一个包含所有被传入进来的关键字参数的字典

```python
def enroll(name, age, city='BeiJing', skill='python'):
    l = {'name':name, 'age':age, 'city':city, 'skill':skill}
    return l

teacher = enroll('ziawang', 23, skill='stock')
student = enroll('liuxiaoqian', 23, city='lichuan', skill='education')


def info(person, **kw):
    tup = (person, kw)
    return tup
student_info = info('student', **student)           # 调用函数的时候，记得把  **  加上，不然解释器无法识别传入的参数是否是关键字参数，会报错。(可变参数也是要加上*)
teacher_info = info('teacher', **teacher)
print(student_info)
print(teacher_info)
```

> 输出结果

```python
('student', {'name': 'liuxiaoqian', 'age': 23, 'city': 'lichuan', 'skill': 'education'})
('teacher', {'name': 'ziawang', 'age': 23, 'city': 'BeiJing', 'skill': 'stock'})
```

##命名关键字参数
- 为什么要有命名关键字参数
	- 定义函数中**kw所代表的关键字参数的传入的参数名和参数数量都是任意的，用命名关键字参数可以限制关键字参数
- **命名关键字可以有缺省值（默认值），方便函数调用**
- 定义方式
	- 第一种情况，如果参数列表中没有可变参数*args，     
		- def f(a, b, *, 命名关键字1,命名关键字2)
	- 第二种情况，如果参数列表中有可变参数，将  * 替换成*args即可

```python
def fun1(name, age, *, city='beijing', skill):
    print(name, age, city, skill)

fun1('ziawang', 23, skill='stock')

def fun2(name, age, *args, city='beijing', skill):
    for i in args :
        print(i)
    print(name, age, city, skill)

say = [5, 4, 3, 2, 1, 'show time !']
fun2('ziawang', 23, *say, city='BoZhou', skill='stock')
```

> 输出结果


```python
ziawang 23 beijing stock
5
4
3
2
1
show time !
ziawang 23 BoZhou stock
```

- 注意：
	1. 指定缺省值的命名关键字参数可以在函数调用时不再另指定，此时就会使用函数指定的默认缺省值。
	2. 命名关键字的调用必须使用关键字调用方式

```python
>>> def func(a, *, block):
	pass

>>> func(1, True)
Traceback (most recent call last):
  File "<pyshell#8>", line 1, in <module>
    func(1, True)
TypeError: func() takes 1 positional argument but 2 were given
>>> func(1, block=True)
```



**关键字参数和可变参数获得的只是一个副本，不是源对象.而且对于可变参数来说，其本身传入的参数被包装成了一个tuple，就成了一个不变对象，改变其元素值的代码会报错**

```python
def fun(*args):
    for i in args:
        i = 'haha'
    return args

l = [1, 2, 3, 4]
print('传入参数后，args变成了这样： ',fun(l))
print('l本身并没有被改变： ',l)
```
> 运算结果

```python
l的副本变成了这样：  ([1, 2, 3, 4],)
l本身并没有被改变：  [1, 2, 3, 4]
```

## 参数列表的分拆
- **定义函数时，*代表参数的聚合**
- **调用函数时，*代表参数的拆解**



> 将函数的参数批量的传入函数参数列表中（要与前面关键字参数区分开）

- 当我们要传递的参数在一个列表中或者一个字典中时候，而调用的函数却接受分开的一个个的参数值，这个时候我们可以把已有的列表用操作符*或者**拆开来。
- 当调用函数时，传入的f(1,2,3,4,5)或f(a=1, b =2, c= 3)中散列的数据被python打包成一个元组或字典，然后在执行函数时，被*拆解开调用

```python
def ccc(*args):
    print(args)
    # def inner(a,b,c,d,e):
    #     print(a,b,c,d,e)
    def inner(args):
        print(args)
    inner(args)         # *只有一个解压的作用，将变量压缩成tuple是python解释器完成的，而不是*压缩的
ccc(1,2,3,4,5)

```
- 在调用函数时候，加一个*或**来自动把参数列表拆开

```python
>>> list(range(3, 6))
[3, 4, 5]
>>> args = (3, 6)
>>> list(range(*args))
[3, 4, 5]
>>>

```
```python
def parrot(votage, state='a stiff', action='voom'):
    print("-- This parrot wouldn't ", action, end= ' ')
    print("if you put ", votage, "volts through it .", end=" ")
    print("E's", state, "!")
dic = {"votage":"four millon", "state":"bleein' demised", "action":"VOOM"}
parrot(**dic) 
```

> 运行结果

```python
-- This parrot wouldn't  VOOM if you put  four millon volts through it . E's bleein' demised !
```

## 匿名函数lambda
格式：
	`lambda  参数 ： 函数体`

- 函数体可以是要返回的返回值，也可以是一个表达式

```python
l = [1, 2, 3, 4, 5, 6]
l_new = list(map(lambda i:str(i), l))
print(l_new)

```

- 当需要定义函数的时候，如果函数比较短小，应该首先考虑lambda函数
- 比如在进行sorted（）函数排序的时候

```python
 a = [(1, 2), (4, 1), (9, 10), (13, -3)]
a.sort(key=lambda l : l[0], reverse=False)  # 或者  sorted(a, key=lambda l: l[0], reverse=False)
print(a)

#[(1, 2), (4, 1), (9, 10), (13, -3)]
```

- 通过lambda关键字可以创建短小的匿名函数，可以用于任何需要的函数对象
- 只能有一个单独的表达式
- **lambda形式的匿名函数可以从外部作用域引用变量**
- **特别注意！！**   

```python 
>>> x = 10
>>> a = lambda y: x+y
>>> a(10)
20
>>> x = 20
>>> a(10)
30
>>> 
```
- 上面这个例子，可以看出来，随着x值的变化，lambda函数中x的值也是不断变化的。lambda函数中的x是一个自由变量，在运行的时候绑定值，而不是在定义的时候绑定值。（这一点和函数定义完全不同）
- 如果想让匿名函数在定义的时候就捕获到值，可以为lambda函数的参数设定默认值。举个栗子

```python
>>> x = 10
>>> a = lambda y: x+y
>>> a(10)              #这里，a传入的实参对应的是匿名函数定义的参数列表中的y
20
>>> x = 20
>>> a(10)
30
>>> x = 10
>>> a = lambda y, x=x: x+y
>>> a(10)
20
>>> x = 20
>>> a(10)
20
>>> b = lambda y,x=x: x+y
>>> b(10)
30
>>> 

```

- 在生成式中使用lambda函数的时候，要注意变量是不断变化的，看下面

```python
>>> funcs = [lambda x:x+n for n in range(5)]
>>> for i in funcs:
	print(i(0))

	
4
4
4
4
4
>>> funcs = [lambda x, n=n:x+n for n in range(5)]
>>> for i in funcs:
	print(i(0))

	
0
1
2
3
4
>>> 


a = [lambda x:x+i for i in range(10)]
def func():
    l = []
    for i in range(10):
        def inner(x):
            return i+x
        l.append(inner)
    print(i)
    return l
res = func()
print(res[0](10))

```





- lambda返回一个值和函数

```python
>>> f = lambda x,y:x+y
>>> f
<function <lambda> at 0x0000020B4A94F1E0>
>>> f(1, 2)
3
>>> def make_incrementor(n):
	return lambda x: x+n                             #lambda作为返回值，则函数make_incrementor返回的还是一个函数，看下面

>>> make_incrementor(1)
<function make_incrementor.<locals>.<lambda> at 0x0000020B4A94F268>
>>> make_incrementor(1)(2)
3
>>> pairs = [(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
>>> s = sorted(pairs, key=lambda pair:pair[1])    # 嵌套进其他函数中，非常实用
>>> s
[(4, 'four'), (1, 'one'), (3, 'three'), (2, 'two')]
>>> 
```
##  三元运算符
- 用处：嵌套到其他语句中执行返回的

```python
l = [i**2 if i%2==0 else i**3]
```

- 格式一：
	- if的返回语句    if   if的条件语句    else     else的返回语句
	- 首先你都去if的条件语句，如果为真，那么就运行True返回语句，否则运行False的反水语句

```python
>>> '满足if条件.....执行....'  if False else  print  '满足else条件.....执行...'
满足else条件.....执行...
---------------------
 def func(a, b ):
     max = a if a>b else b
     return max
 print(func(1,2))
```

- 格式二
	- （else的返回内容， True的返回内容）[条件语句]
	- 条件语句为真，则返回True的内容，反之返回False的内容

```python

>>> (('这是else的执行语句'), ('这是True的执行语句'))[True]
'这是True的执行语句'
```

- **注意** ：格式二的返回语句需要有返回值，如果用print就会出现下面情况。
- **在平常使用中应尽量使用第一种格式**
	- 不适用元组条件表达式的原因是因为元组中是先建数据，然后用True(1),False(0)来所引导数据。而第一种格式的if--else条件表达式遵循普通的if-else逻辑树，所以尽量避免元组条件表达式

```python
>>> (print('这是else的执行语句'), print('这是True的执行语句'))[True]
这是else的执行语句
这是True的执行语句		#两句都执行了


condition= True
print(2 if contion else 1/0)
print((1/0, 2)[condition])
```

- 三元运算符可以嵌套使用

```python
a,b,c = 10,20,5
min_Value = a if a<b and a<c else (b if b<a and b<c else c)
print(min_Value)

----------------------------------------------------------------------------------------------------------
#等价于

tup = a, b ,c = 10,20,5
def min_value(x, y, z):
    if x<y and x<z:
        return x
    else:
        if y<x and y<z:
            return y
        else:
            return z

print(min_value(*tup))

```
