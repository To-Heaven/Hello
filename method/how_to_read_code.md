# 如何正确阅读源码

#### 0. 了解类实例化的完整流程

- `对象`是由`类`创建的，而类则是由元类(type)创建的。
- 当类由`type元类`创建时，我们在代码中使用`class Foo`关键字创建一个`Foo类时`时，就会自动调用`type`元类的`__init__`方法，当`Foo`实例化得到一个对象的时候，就会执行`type`中的`__call__`方法，在该方法内部存在这样一个流程
	1. 调用`Foo`的`__new__`方法
	2. 调用`Foo`的`__init__`方法
	3. 返回`__new__`方法得到的实例


```python
class MyType(type):
    def __init__(self, *args, **kwarg):
        print(self)
        super().__init__(*args, **kwarg)

    def __call__(self, *args, **kwargs):
        obj = self.__new__(self, *args, **kwargs)
        self.__init__(obj, *args, **kwargs)
        return obj

class Foo(object, metaclass=MyType):
    def __init__(self):
        print('in Foo init')

    def __new__(cls, *args, **kwargs):
        print('in Foo new')

Foo()
```

#### 1. 搞清楚`self`是谁
- 在源码中，经常会遇到类的复杂继承，有一种经常遇到的情况。


```python
class Par(object):
    def f1(self):
        self.f2()			# Par.f2(s)

    def f2(self):
        print('par f2')


class Sub(Par):
    def f2(self):
        print('foo f2')


s = Sub()
s.f1()						# Sub.f1(s)
```

- 上述例子中
	1. `s.f1()`回到`Par`类中找`f1()`，并执行该方法
	2. 在`s.f1()`内部执行了`self.f2()`这个时候，**一定要搞清楚当前的`self`是`Sub`的实例对象`s`，而不是`Par`的实例对象**，因此执行`self.f2()`的时候会再回到`Sub`类中去寻找`f2`方法，如果`Sub`中没有定义`f2`方法，才会执行`Par`中的f2方法

- 补充（关于函数与方法）
	- `Sub`中，`self.f2()`其实就可以看作`Sub.f2(s)`
	- 上述代码执行过程中，`s.f1()`就可以看作`Par.f1(s)`


#### 2. 对象使用句点符的时候有几种情况
- 比如`obj.xxx`
- 你不能一上来就说这个`xxx`一定是`obj`的属性。事实上在python中，有四种情况需要我们区分
	1. `xxx`确实是`obj`的一个属性
	2. `xxx`是通过`@property`装饰的一个函数名，比如`Django路由系统中的"site.urls"`实际上对应的是一个`config_obj..urls`方法
	3. `xxx`可能是类的一个静态字段
	4. `xxx`可能不是`obj`所在类的一个静态字段，有可能是其父类或其他类中的静态字段

