# 类和对象

- 类就是一个模板，在类中定义了一些函数，函数实现特定功能
- 实例对象就是类实例化得到的对象，可以通过实例对象来调用类中的属性和方法

# 静态存储和动态存储

- 静态存储方式
	- 程序在运行期间分配固定的存储空间的方式
- 动态存储方式
	- 程序在运行期间根据需要进行动态分配存储空间的方式



# 类的成员
- 字段
- **普通字段和静态字段在内存中保存的位置不同** 
	- 静态字段（类属性）
		- 属于类
		- 静态字段的访问   
			- 类名.字段名
		- 在内存中只保存一份
		- 类的实例对象都有相同的普通字段时，可以创建静态字段代替重复的普通字段

	- 普通字段（对象属性）
		- 属于对象
		- 普通字段的访问
			- 对象.字段名
			- 类名不能访问普通字段，因为普通字段的不存储在类中
		- 每个对象中都保存一份普通字段 
- 方法（所有的方法在内存中都属于类）
	- 普通方法
		- 至少一个self参数
		- 调用该方法的时候，自动将调用该方法的对象赋值给self
		- 当通过类名调用普通该方法的时候，参数列表中的第一个参数就会被赋值给self位置参数（避免这种情况可以创建一个静态方法@staticmethod）
	- 类方法
		- @classmethod
		- 类调用，至少一个cls参数，执行类方法的时候，自动将调用该方法的的类复制给cls
		- 实例对象也可以调用类方法

```python
class Foo:
    def __init__(self):
        pass

    def func_instance(self):
        print(self.func_instance.__name__)

    @classmethod
    def func_class(cls):
        print(cls.func_class.__name__)

instance = Foo()
instance.func_instance()
instance.func_class()
Foo.func_class()

```

- 静态方法 
	- 无默认参数cls和self
	- 类和对象都可以调用

```python
class Foo:
    def __init__(self):
        pass

    def func_instance(self):
        print(self.func_instance.__name__)

    @classmethod
    def func_class(cls):
        print(cls.func_class.__name__)

    @staticmethod
    def static_func():
        print('静态方法 ')
instance = Foo()
instance.func_instance()
instance.func_class()
Foo.func_class()

Foo.static_func()



```

- 属性
	- 普通属性
		- 属性其实就是方法的变种
	- 属性的两种定义方式
		1. 在类的构造函数__init__中。
		2. 使用  property装饰器
			- 在普通方法上添加@property装饰器
			- 该方法只能有self一个参数
	- @property在经典类中创建属性
		- return 属性值

```python
# 经典类中
class Foo:

    @property
    def attr(self):
        return '普通属性'

s = Foo()
print(s.attr)
----------------------------------------
普通属性
```


常用方法

```python
from math import pi

class Demo:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return pi*self.radius**2

    @property
    def perimeter(self):
        return 2*pi*self.radius

d = Demo(1)
print('area: ', d.area)
print('perimeter: ', d.perimeter)

----------------------------------------------------------------------------
# 运行结果
area:  3.141592653589793
perimeter:  6.283185307179586



```


如果要附加赋值和删除功能

```python

class Demo:

    @property
    def attr(self):
        return self.value
    @attr.setter
    def attr(self, value):
        self.value = value

    @attr.deleter
    def attr(self):
        del self.value


d = Demo()
d.attr = 'value'
print(d.attr)
d.attr = 'new_value'
print(d.attr)

del d.attr
print(d.attr)

---------------------------------------------------------------------------------------
# 运行结果
value
new_value
Traceback (most recent call last):	
  File "D:/files/python_practice/20170814-0820/0815/practice.py", line 107, in <module>
    print(d.attr)
  File "D:/files/python_practice/20170814-0820/0815/practice.py", line 90, in attr
    return self.value
AttributeError: 'Demo' object has no attribute 'value'
```




		
- 注意
	1. 所有成员中，只有普通字段的内容保存在对象中，即创建了多少个对象内存中就有多少个普通字段
	2. **除了普通字段的成员都是保存在类中**
		- 方法、类属性存放在类内存空间中
	3. 由上述可知，不论对象的多少，上述字段方法属性都只在内存中创建一份

# 定义类
- 注意
	1. 类名首字母大写
	2. 类名后面要有冒号
	3. 类的内容要缩进在类的代码范围内

```python
class 类名:
    类属性 = '类属性是个静态变量'
    
    def __init__(self, 实例属性1, 实例属性2):
        self.实例属性1 = 实例属性1
        self.实例属性2 = 实例属性2
        
    def 方法名(self):
        pass
    
```

## 内置类属性和方法
```python
for i in dir(类名)：
	print(i)
----------------------------------
__class__
__delattr__
__dict__
__dir__
__doc__
__eq__
__format__
__ge__
__getattribute__
__gt__
__hash__
__init__
__init_subclass__
__le__
__lt__
__module__
__ne__
__new__
__reduce__
__reduce_ex__
__repr__
__setattr__
__sizeof__
__str__
__subclasshook__
__weakref__
方法名
类属性
```

- classname.__dict__
	- 返回类的字典，key是属性名或方法名，value是属性值或方法内存地址

- classname.__name__ 
	- 返回一个字符串，存储着类的名字

- classname.__doc__
	- 返回一个类的文档字符串

- classname.__base__
	- 返回该类的第一个父类 


- classname.__bases__ 
	- 返回一个元组，存放着该类的所有父类

- classname.__module__
	- 返回类所在的模块名，如果是在定义该类的模块中调用，返回 __main__

- obj.__class__
	- 实例对应的类

```python
print(类名.__name__)
print(类名.__dict__)
print(类名.__bases__)
print(类名.__name__)
print(类名.__module__)
print(类名.__class__)

---------------------------------------------------------------------------------------------------------------
类名
{'__module__': '__main__', '类属性': '类属性是个静态变量', '__init__': <function 类名.__init__ at 0x000001517A01B950>, '方法名': <function 类名.方法名 at 0x000001517A01B8C8>, '__dict__': <attribute '__dict__' of '类名' objects>, '__weakref__': <attribute '__weakref__' of '类名' objects>, '__doc__': None}
(<class 'object'>,)
类名
__main__
<class 'type'>
```


# 类的作用
- 属性引用（类名.属性）
	- 但是只会调用类中定义的类属性，但调用不到实例对象的属性
	- 类名调用方法名（而不是调用方法），会返回方法的内存地址
- 实例化
	- 类名加上括号，就是实例化——创建类实例的过程。
	- 实例化过程会自动触发__init__函数的运行，可以用__init__来个性化定义实例
	- obj = classname()     <==>      obj = classname.__init__()

```python
class demo:
    def __init__(self, name):
        print('这个函数中的代码会在实例化的过程中执行')
        if name.isalpha():
            self.name = name
        else:
            print('姓名包含非字母')

ziawang = demo('9ziawang')

--------------------------------------------------------------
这个函数中的代码会在实例化的过程中执行
姓名包含非字母
```


### self
- 类定义中，self就代表着实例对象， 这个名字不固定，但是建议不要更改，就因该写self

# 类与对象
## isinstance
- 用来判断一个实例是否是由指定的类创建的

```python

class demo:
    def __init__(self, name):
        print('这个函数中的代码会在实例化的过程中执行')
        if name.isalpha():
            self.name = name
        else:
            print('姓名包含非字母')

ziawang = demo('ziawang')

ret  = isinstance(ziawang, demo)
print(ret)
-------------------------------------------------
这个函数中的代码会在实例化的过程中执行
True
```

# 对象
## 对象的作用
- 对象只有一个作用，引用属性
	- 方法其实也算是对象的属性，成为动态属性
	- 单单引用方法名时，不会调用这个方法，只有在方法名后面加上括号才会调用这个方法


# 类对象与实例对象的命名空间
- python给类在内存中分配了独立的内存空间，用来存放类属性和类中定义个各种方法。
- 对象创建时，在类内存之外独立拥有一个内存空间，用于存放对象属性和使用setattr动态定义的属性和方法。
- 对象调用方法时因为在其内存空间存在了一个指向了类内存中方法地址的一个引用，当对象调用其方法的时候本质上就是通过这个引用调用类中的方法。

## 属性
- 类属性定义在该类的命名空间中，假设现在创建了个    role = 'human' 的类属性
- 当一个实例对象创建时候，就在该类的命名空间之外创建了一个新的命名空间。	
- 当实例对象调用属性的时候，python解释器会首先在该实例对象的命名空间中寻找该属性名，如果没有找到，就会继续去类命名空间中寻找该属性名，查看是否存在该类属性，如果类命名空间中也没有该属性，就去该类的父类中去寻找，如果父类中也没找到，python解释器就会报错。
	- 类创建的时候，会在内存中开辟一个内存空间，存放类属性和方法
	- 对象创建时候，会在内存中类内存空间之外创建一个内存空间，用于存放实例对象的属性和实例方法。


## 方法
- 当一个实例对象创建完成之后，就在内存空间中创建了一个该实例对象自己的内存空间，该内存空间中存放着实例对象的属性和指向类内存空间中实例方法的地址，即类对象指针。
	- 方法是在类内存空间中存放的，实例对象内存中存放着指向类内存空间中方法的类对象指针，该指针指向类中的方法

```python

class demo:
    def __init__(self, name):
        if name.isalpha():
            self.name = name
        else:
            print('姓名包含非字母')
    def func1(self):
        pass
ziawang = demo('ziawang')
haha_girl = demo('haha')
print('demo.func : ', demo.func1, id(demo.func1))
print('ziawang.func: ', ziawang.func1, id(ziawang.func1()))
print('haha_girl.func: ', haha_girl.func1, id(haha_girl.func1()))

-----------------------------------------------------------------------------------------------------
demo.func :  <function demo.func1 at 0x000002CFE071BA60> 3091847035488
ziawang.func:  <bound method demo.func1 of <__main__.demo object at 0x000002CFE072A198>> 1768555728
haha_girl.func:  <bound method demo.func1 of <__main__.demo object at 0x000002CFE072A240>> 1768555728
```




## 动态创建实例对象属性
 - 在类的定义空间之外，可以动态的临时创建实例对象的属性

```python

class Birth:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day


bir = Birth('1994', '9', '2')
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
        # self.birthday = bir

ziawang = Person('wangzihao', '21')
ziawang.birthday = bir
print(ziawang.birthday.year)

```

http://www.jb51.net/article/65300.htm
## object
- 所有类的父类


## 软件重用
## 类之间的组合使用
- 组合是指在一个类中将另一个类的对象作为**这个类或者这个类的实例对象**的属性

```python

# 作为类属性


class Birth:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day


bir = Birth('1994', '9', '2')
class Person:
    birthday = bir
    def __init__(self, name, age):
        self.name = name
        self.age = age

ziawang = Person('wangzihao', '21')
print(ziawang.birthday.year)
haha = Person('haha', '1')
print(haha.birthday.year)
------------------------------------------------------------------------------------------------------------------------
# 作为实例对象的属性
class Birth:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day


birthday = Birth('1994', '9', '2')
class Person:
    def __init__(self, name, age, birthday):
        self.name = name
        self.age = age
        self.birthday = birthday

ziawang = Person('wangzihao', '21')
print(ziawang.birthday.year)

```


### 格式
### 在子类中执行父类方法的三种方式及优点
- super关键字
	- 只在python3中存在

```python
class Animal:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def eat(self):
        print('eating')

    def sleep(self):
        print('sleeping')


class Dog(Animal):
    def __init__(self, name, age):
        # Animal.__init__(self, name, age)  # 第一种方式			# python2 中，必须写  self和参数
        super(Dog, self).__init__(name, age)    # 第二种方式
        # super().__init__(name, age)         # 第三种方式
    def bite(self):
        print('biting someone. . . , then sleep . . . ')
        super().sleep()

d = Dog('2ha', 2)
print(d.name)
print(d.age)
d.bite()
# super调用也可以在类定义之外
print('-'*30)
super(Dog, d).sleep()
```




 
### 派生
- 父类中的属性方法不能满足子类功能和属性的需要，子类就可以创建新的属性或方法，或者定义一个相同名称的方法，覆盖父类方法
#### 在父类方法基础上派生
- 在子类派生的方法中配合使用super()来实现保留父类功能的基础上进一步扩展



```python


class Cls:
    def print_hello(self):
        print('hello class:', self.__class__)

class Subcls(Cls):
    '''
    这是一个Cls的子类，可以扩展父类方法功能，或者增加新的功能
    '''
    def print_hello(self):
        super().print_hello()
        print('还可及继续添加其它功能')

    def func(self):
        print(self.__doc__)

c = Cls()
s = Subcls()
c.print_hello()
s.print_hello()
s.func()

------------------------------------------------------------------------
# 运行结果

hello class: <class '__main__.Cls'>
hello class: <class '__main__.Subcls'>
还可及继续添加其它功能

    这是一个Cls的子类，可以扩展父类方法功能，或者增加新的功能
```

# 继承
## 经典类与新式类
- 经典类：当前类或者父类**不继承自**object类
- 新式类：是python中object的子类

- python2中
	- 默认创建的类是经典类
	- 在新创建的类名后面括号中手动继承object类

## 接口类和抽象类
- 类：从多个对象中抽取出相同的内容创建来的
- 抽象类：本质上还是类，是一组类的相似性，包括数据属性和函数属性， 从多个类中抽取出相同的内容
- 接口类：
### 接口类
- 用途
	- 继承接口类实际上是规定了一个兼容接口，使得外部调用者不需要关心具体细节，可以一视同仁的处理实现了特定接口的所有对象————归一化


- 注意事项
	- 实现接口类需要借助abc模块的ABCMeta元类和abstractclassmethod装饰器
	- 继承接口类的时候，鼓励使用多继承 
	- 在接口类中，任何一种方法都是规范，所以不能实现接口类中的方法，具体的功能需要子类去实现
	- 接口隔离原则：使用多个专门的几口，而不是单一的总接口，即客户端不应该依赖那些不需要的接口
	- 如果想写一个接口类，就不要实现接口类中的方法，虽然可以实现而且不会报错

```python
from abc import ABCMeta, abstractmethod

class Interface_class(metaclass=ABCMeta):

    @abstractmethod
    def func(self):
        print('Interface_func........')
        # pass


class A(Interface_class):
    def func(self):
        Interface_class.func(self)
        print('A.......func')

a = A()
a.func()
----------------------------------------------------------------------------------
# 运行结果

Interface_func........
A.......func


```


- 接口类举例
**如果一个继承了接口类的子类没有对接口类中兼容的方法或属性， 那么这个子类在创建对象的时候就会报错，而不是调用方法时才报错，这可以避免创建非法对象**

```python
# 1. 当在子类中找不到对应方法时候，会去父类中寻找
class Animal:
    def run(self):
        pass

class Dog(Animal):
    def dog_run(self):
        print(' dong runing....')

def run_func(obj):
    obj.run()

d = Dog()
run_func(d)  #  运行结果为None

-----------------------------------------------------------------------
# 2. 可以在父类中使用raise方法，这样就能检测子类中是否有该方法，可是这个时候这个错误的类已经定# 义对象了
class Animal:
    def run(self):
        raise NotImplementedError		# 当子类找不到方法（没有对应方法）时候，会通过父类方法报错

class Dog(Animal):
    def dog_run(self):
        print(' dong runing....')

def run_func(obj):
    obj.run()

d = Dog()
run_func(d)

# 3. 还有一种就是将父类定义成接口类
from abc import ABCMeta, abstractclassmethod

class Animal(metaclass=ABCMeta):
    @abstractclassmethod
    def run(self):
        pass

class Dog(Animal):
    def dog_run(self):
        print(' dong runing....')

def run_func(obj):
    obj.run()

d = Dog()			# 在创建该类的对象时候就会报错。
# run_func(d)


# ---------注意--------------
from abc import ABCMeta, abstractclassmethod, abstractmethod

class Payment(metaclass=ABCMeta):
    '''接口类不能被实例化， 规范了支付功能都必须实现pay方法'''
    @abstractmethod
    def pay(self):      # 抽象类子类的定义中， 子类方法参数竟然还可以不一样
        pass

class Alipay(Payment):
    def pay(self, money):
        print('支付宝支付%s元'%money)

class Applepay(Payment):
    def pay(self, money):
        print('apple pay支付%s元'%money )

def pay(payment, money):
    payment.pay(money)

a = Alipay()
pay(a, 200)

----------------------------------------------------------------------------------------------------
# 结果
支付宝支付200元



```

### 抽象类
- 用途
- 注意事项
	- **抽象类只能被继承，不能被实例化**
	- 抽象类也需要借助abc模块的ABCMeta，abstractclassmethod来实现
	- 抽象类中只能有抽象方法，该方法中可以定义功能，为一些抽象方法做出基础实现
	- 继承抽象类的过程中，尽量避免多继承
	- **抽象类定义的抽象方法，必须子类中实现该方法**
- 举个例子


```python
from abc import ABCMeta, abstractmethod
class Abstract_Class(metaclass=ABCMeta):
    @abstractmethod
    def func(self):
        print('抽象类的基础功能')
        pass

class Child_class(Abstract_Class):
    def func(self):
        super().func()
        print('这是子类扩展功能')

c = Child_class()
c.func()
-------------------------------------------------------
#输出结果
抽象类的基础功能
这是子类扩展功能



----------------------------------------------
# 如果继承了抽象类的子类没有实现父类的抽象方法，在创建子类对象的时候 ， 就会报错
from abc import ABCMeta, abstractclassmethod
class Abstract_Class(metaclass=ABCMeta):
    @abstractclassmethod
    def func(self):
        print('抽象类的基础功能')
        pass

class Child_class(Abstract_Class):
    def my_func(self):
        super().func()
        print('这是子类扩展功能')

c = Child_class()
# c.func()
---------------------------------------------------------------------------------------
TypeError: Can't instantiate abstract class Child_class with abstract methods func
```

## 注意
- 在继承抽象类的过程中，我们应该尽量避免多继承，因为抽象类中的方法是可以实现部分基础功能的，继承多个抽象类会使得子类调用该方法时候出现混乱，不同的集成顺序会调用不同的基础功能（广度优先）
- 而在继承接口的时候，鼓励使用多继承，是因为接口的方法是不能有任何功能实现的， 即使在多个接口类中定义了该方法，都不会使其子类出现继承不同功能的情况，因为接口类中的功能是没有实现的


## 单继承和多继承
- 继承的作用
	1. 继承基类的方法，并作出自己的功能的扩展
	2. 声明某个子类兼容于某个基类，定义一个接口类interface。接口类中定义了一些接口名（就是定义了一些函数名），这些定义在基类中接口是未实现功能的。通过子类继承该接口类，扩展基类接口中的功能。
### 单继承
### 多继承
### 钻石继承


## 广度优先算法和深度优先算法
### 广度优先
### 深度优先



# 封装
- 封装的目的


## 类中的私有变量
-  设置私有对象属性
	-  在对象属性名的前面添加上双下划线__	，可以使该属性变成一个私有属性 
	-  变成私有属性的属性名称其实是被python更改了
		-  self.\_\_attr     < = = >   self._classname__attr
	- 私有属性在本类中可以正常使用，在本类之外使用就必须使用  _classname__attr调用
- 在外部修改私有对象属性
	- 推荐使用：在本类中定义方法修改\_\_attr
	- 不推荐使用_class__attr的方法


```python
class Demo:
    def __init__(self, value):
        self.__value = value

    def get_value(self):
        return self.__value

    def set_value(self, new_value):
        self.__value = new_value
        return self.__value


obj = Demo('value')
try:
    print('try :  obj.__value = ', obj.__value)
except:
    print('not try, except:  obj._emo__value', obj._Demo__value)

print('obj.get_value()', obj.get_value())
obj.set_value('new_value')
print('obj.get_value()', obj.get_value())
----------------------------------------------------------------------------------------------------
not try, except:  obj._emo__value value
obj.get_value() value
obj.get_value() new_value
```


- 外部获取私有类属性
- 设置私有类属性
- 在外部修改私有类属性
- 在外部获取私有类属性

```python
class Goods:

    __discount = 0.8

    def __init__(self, name, price):
        self.name = name
        self.price = price

    @classmethod
    def set_discount(self, new_discount):
        self.__discount = new_discount
        return self.__discount

    def real_price(self):
        return self.price * Goods.__discount

g = Goods('banana', 1.99)

real_price = g.real_price()
print(real_price)
Goods.set_discount(0.5)
real_price = g.real_price()
print(real_price)
```

### 私有变量
	- 私有类属性
	- 私有方法
	- 私有对象属性
- 对外保护属性、方法
- 对子类保护属性和方法



### 父类中定义私有方法
- 不想让子类继承的属性和方法，可以在父类中设置成私有变量
	- 但是子类仍然可以使用  _Farclassname__func()


```python
class Fu:
    def __func(self):       # __func <==>  _Fu__func()
        print('Fu   _Fu__func() runing ')


class Son(Fu):
    pass


s = Son()
s._Fu__func()
s.func()
--------------------------------------------------------------------------------
# 运行结果
Fu   _Fu__func() runing 
Traceback (most recent call last):
  File "D:/files/python_practice/20170814-0820/0815/practice.py", line 58, in <module>
    s.func()
AttributeError: 'Son' object has no attribute 'func'
```

- 看例子

```python
class Fu:
    def __init__(self):
        self.__func()

    def __func(self):       # __func <==>  _Fu__func()
        print('Fu   _Fu__func() runing ')


class Son(Fu):
    pass
s = Son()

--------------------------------------------------------------
#运行结果
Fu   _Fu__func() runing
```

- 和私有对象属性一样，私有类方法也是将方法名更改成了   _classname__func()
注意1：

```pyhton
class Fu:
    def __init__(self):
        self.__func()

    def __func(self):       # __func <==>  _Fu__func()
        print('Fu   _Fu__func() runing ')


class Son(Fu):
    def __func(self):
        print('Son  _Son__func() runing ')

s = Son()

------------------------------------------------------------
# 运行结果
Fu   _Fu__func() runing 
```

注意2：

```python
class Fu:
    def __init__(self):
        self.__func()

    def __func(self):       # __func <==>  _Fu__func()
        print('Fu   _Fu__func() runing ')


class Son(Fu):
    def _Son__func(self):
        print('Son  _Son__func() runing ')

s = Son()

# 结果
Fu   _Fu__func() runing 
```



### 私有属性的应用
- 保存临时数据（以缓存为例）


```python
from urllib.request import urlopen

class WebPage:
    def __init__(self):
        self.__content = None

    def get_page(self, url):
        if self.__content:
            return self.__content
        else:
            self.__content = urlopen(url).read()
            return self.__content

w = WebPage()
w.get_page('https://www.baidu.com')
```

不过推荐下面写法

```python
from urllib.request import urlopen

class WebPage:
    def __init__(self, url):
        self.url = url				# 把url这个参数放在构造函数中更好
        self.__content = None

    def get_page(self):
        if self.__content:
            return self.__content
        else:
            self.__content = urlopen(self.url).read()
            return self.__content

w = WebPage('https://www.baidu.com')
w.get_page()
```

或者用装饰器

```python
from urllib.request import urlopen

class WebPage:
    def __init__(self, url):
        self.url = url				# 把url这个参数放在构造函数中更好
        self.__content = None
    
    @property
    def content(self):
        if self.__content:
            pass
        else:
            self.__content = urlopen(self.url).read()
        return self.__content

    # @property
    # def content(self):
    #     if not self.__content:
    #         self.__content = urlopen(self.url).read()
    #     return self.__content

w = WebPage('https://www.baidu.com')
print(w.content)
```


## 类中方法的种类和区别
- 绑定方法和非绑定方法
	- 绑定方法： 普通方法，类方法@classmethod
		- 普通方法：默认要有一个self参数，并且普通方法只能被对象调用
		- 类方法：默认有一个cls参数来表示本类，可以被类和对象使用
	- 非绑定方法：静态方法 @staticmethod
		- 静态方法：不需要传入方法，可以被类和对象调用


>  thereof          其它的



## isinstance(object, classinfo)
- return True  
	- if the object argumentis an instance of the classinfo argument, or of a subclass thereof(direct, indirect or virrtual)
	- if classinfo is a tuple of type objects , return True if object is an instance of any of the types.
		- if classinfo is not a type or tuple of types and such tuples, a TypeError will raised

```python

class A:
    pass

class B:
    pass

class C(A):
    pass

c = C()

print(isinstance(c, A))
print(isinstance(c, (A, B)))
print(isinstance(c, object))
print(isinstance(c, (1, 2)))
------------------------------------------------------------------------------------------
# 运行结果
True
True
True
Traceback (most recent call last):
  File "D:/files/python_practice/20170814-0820/0816/practice.py", line 22, in <module>
    print(isinstance(c, (1, 2)))
TypeError: isinstance() arg 2 must be a type or tuple of types

```


## issubclass
- return True
	- if class is a subclass of classinfo 
	- Note: a class is considered as subclass of itself
	- classinfo can be a tuple of class objects.

```python

class A:
    pass

class B:
    pass

class C(A):
    pass



print(issubclass(C, C))
print(issubclass(C, A))
print(issubclass(C, (A, B)))

----------------------------------------------------------------------------
# 运行结果

True
True
True



```



## 反射（自省）
- 程序可以检测，修改，访问自身状态和行为的一种能力
- python面向对象中的反射：
	- 通过字符串的形式来操作对象的属性（对象的属性以字符串的形式被执行相关操作）
	- **python中的一切事物都是对象， 包括模块，类等等**
		- 可以利用反射来操作模块中的功能
- hasattr(object, name)
	- the argument is a object and a string . 
	- return True:
		- if the string is the name of one the obect's attribute. 
	- how to implement
		- this is implemented by calling getattr(obj, name), seeing whether it raise an AttributeError or not 


- getattr(object, name [default])
	-  name must be a string .
	-  return the value of the named attribute of object  
	-  if the string is the name of on of the object's attributes , the result is the value of that attribute.
		- getattr(obj, 'name')     <==>     obj.name 
	-  Note: **if the named object is not exist, default is returned if provided, otherwise AttributeError is raised**
	-  注意：getattr()的参数name是一个方法的名称的时候，getattr返回值是一个方法的内存地址

```python
class A:
    def __init__(self, a, b, c):
        self.a = a
        self.b = b
        self.c = c

    def say_hello(self):
        print('hello, i am a method of object')

obj_a = A('attr_a', 'attr_b', 'attr_c')
method = getattr(obj_a, 'say_hello')
print(method)
method()

---------------------------------------------------------------------------------------------
# 运行结果
<bound method A.say_hello of <__main__.A object at 0x00000137364B9668>>
hello, i am a method of object

```



- setattr(object, name, value)
	- this function assign the value to the attribute, provided the object allows it 
	- name is a string os an existing attribute or a new attribute.


- delattr(object, name)
	- the function delete the named attribute, provided  the object allows it  
	- name must be a string of  one of the object's exist attribute
		- delattr(obj, 'haha')    <==>  del obj.haha

```python
class A:
    def __init__(self, a, b, c):
        self.a = a
        self.b = b
        self.c = c


obj_a = A('attr_a', 'attr_b', 'attr_c')
print(hasattr(obj_a, 'a'))

print(getattr(obj_a, 'a'))
print(getattr(obj_a, 'd', 'obj_a do not has this attribute'))

print('setattr 没有返回值', setattr(obj_a, 'd', 'attr_d'))
print(getattr(obj_a, 'd'))
delattr(obj_a, 'd')
print(obj_a, 'd')

---------------------------------------------------------------------------------------
# 运行结果

True
attr_a
obj_a do not has this attribute
setattr 没有返回值 None
attr_d

Traceback (most recent call last):
  File "D:/files/python_practice/20170814-0820/0816/practice.py", line 49, in <module>
    print(getattr(obj_a, 'd'))
AttributeError: 'A' object has no attribute 'd'
```

- 反射与对象，类的属性字典之间的关系  __dict__
	- 对象的__dict__存放属性的字典中不会存放其可以调用的方法，这是因为定义的对象的方法存放在了类的内存空间中。 **对object的方法使用  hshattr(obj, name)，返回一个True**
	- 使用setattr(obj, name, value)  给对象增加的属性或者方法
		- 该方法能够存放在object.__dict__中，并且可以用hasattr()检测
		- 该方法的参数有self的时候，调用该方法需要传入该对象
	- 不能用delattr删除方法，因为方法存放在类中

```python
class A:
    def __init__(self, a, b, c):
        self.a = a
        self.b = b
        self.c = c

    def say_hello(self):
        print('hello, i am a method of object')


obj_a = A('attr_a', 'attr_b', 'attr_c')

print(A.__dict__)
print(obj_a.__dict__)

def func():
    return'setattr...func'

def method(self):
    return '%s in method'%self.a

setattr(obj_a, 'func', func)
setattr(obj_a, 'method', method)
print(obj_a.__dict__)
ret_func = obj_a.func()
print(ret_func)

ret_method = obj_a.method(obj_a)    # 缺少obj_a回报错
print(ret_method)


delattr(obj_a, 'func')
print(obj_a.__dict__)

----------------------------------------------------------------------------------------------------
# 运行结果
{'__module__': '__main__', '__init__': <function A.__init__ at 0x000001414063C8C8>, 'say_hello': <function A.say_hello at 0x000001414063C950>, '__dict__': <attribute '__dict__' of 'A' objects>, '__weakref__': <attribute '__weakref__' of 'A' objects>, '__doc__': None}
{'a': 'attr_a', 'b': 'attr_b', 'c': 'attr_c'}
{'a': 'attr_a', 'b': 'attr_b', 'c': 'attr_c', 'func': <function func at 0x00000141401D3E18>, 'method': <function method at 0x000001414063C9D8>}
setattr...func
attr_a in method
{'a': 'attr_a', 'b': 'attr_b', 'c': 'attr_c', 'method': <function method at 0x000001414063C9D8>}


```




> counterpart		相似物  
> relative			相对的  

### 可以被反射的对象
- python中的一切对象


### 反射对象为模块
```python

import sys


def module_func():
    print('in modulefunc.......')


print(sys.modules[__name__])

getattr(sys.modules[__name__], 'module_func')()        # 注意调用要加上括号，getattr返回的是一个函数地址
-----------------------------------------------------------------------------------------
<module '__main__' from 'D:/files/python_practice/20170814-0820/0816/practice.py'>
in modulefunc.......

```



### 反射应用方向的了解

```python
class User:
    def register(self):
        print('register.........')

    def sign(self):
        print('signing.............')

unkonw_user = User()
func = input('choose the function of register or sign ： ').strip()
try:
    getattr(unkonw_user, func)()
except:
    getattr(unkonw_user, func)
finally:
    print('end....')

-------------------------------------------------------------------------------
# 运行结果
choose the function of register or sign ： sign
signing.............
end....

```



## 定制类
### __str__和__repr__
- obj.__repr__(self)		(called by the builtin function repr())
	- compute the 'official' string represtion of an object. this could look like a valid python expression that could be used to recreate a object with the same value
	- if this is not possiable, a string of the form (some useful description) should be returned
	- Note: **the returned must be a string object**
	- if class defines a __repr__ without a __str__, when __repr is also used when an 'informal' string represention od instances of that class is required
- object.__str__(self)
	-   The return value must be a string object.



> https://zhidao.baidu.com/question/688119027156731524.html?qq-pf-to=pcqq.c2c

```
>>> import datetime
>>> ti = datetime.datetime.now()
>>> ti
datetime.datetime(2017, 8, 16, 17, 22, 5, 560178)
>>> print(repr(ti))
datetime.datetime(2017, 8, 16, 17, 22, 5, 560178)
>>> print(str(ti))
2017-08-16 17:22:05.560178
>>> print(ti)
2017-08-16 17:22:05.560178
>>> a = eval(repr(ti))
>>> a
datetime.datetime(2017, 8, 16, 17, 22, 5, 560178)
>>> print(a)
2017-08-16 17:22:05.560178
>>> a = eval('datetime.datetime(2017, 8, 16, 17, 22, 5, 560178)')
>>> a
datetime.datetime(2017, 8, 16, 17, 22, 5, 560178)
>>> str(a)
'2017-08-16 17:22:05.560178'
```


>  \_\_del__
>  \_\_getitem\_\_     \_\_setitem\_\_   \_\_delitem__r
>  \_\_new\_\_ 与单例模式
>  \_\_call\_\_
> \_\_len\_\_
> \_\_hash\_\_
> \_\_eq\_\_
> set()去重与\_\_hash__和\_\_eq__








