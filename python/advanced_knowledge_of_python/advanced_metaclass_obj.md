# 元类与获取实例对象的完整流程

### 元类
- `对象`是由`类`创建的，而类则是由元类(type)创建的。
- 我们在代码中使用`class Foo`关键字创建一个`Foo类时`时，就会自动调用其元类的`__init__`方法，当`Foo`实例化得到一个对象的时候，就会执行其元类中的`__call__`方法，在该方法内部存在这样一个流程
	1. 调用`Foo`的`__new__`方法，参数为Foo类，并返回一个Foo的实例
	2. 调用`Foo`的`__init__`方法，将实例作为参数传入并执行
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



### 类实例化流程
1. 创建类时，将类中的所有静态字段和方法加载到内存，然后再执行创建该类的元类的`__init__`方法
2. 实例化`a = Foo()`，会调用元类的`__call__`方法，在该`__call__`中
	1. 调用`Foo.__new__()`
	2. 调用`a.__init__()`


```python
class MyMetaClass(type):
    def __init__(self, name, bases, dic):
        print(name)                 # Foo
        print(bases)                # (<class 'object'>,)
        print(dic)                  # {'__module__': '__main__', '__qualname__': 'Foo', 'a': 'a', 'some_method': <function Foo.some_method at 0x01452DB0>}
        super().__init__(name, bases, dic)


class Foo(object, metaclass=MyMetaClass):
    print('class关键字创建类的时候，静态字段也会被加载')
    a = 'a'

    def some_method(self):
        pass
```


### 使用元类的三种姿势
###### 先说说元类可以用来干什么
- 当我们创建一个类的时候，会首先调用创建该类的元类的`__new__`方法，该方法会返回一个类对象然后再执行元类的`__init__`方法，当这个类实例化的时候，就会执行其元类的`__call__`方法（该方法内部再调用类的`__new__`方法和`__init__`）。

- 那么，既然在创建类之前会执行`创建该类的元类的__new__`方法，那么我们就可以在元类的`__new__`方法中预先对要创建的类进行处理。比如
	- 创建默认静态字段，让每一个类已创建就拥有默认的静态字段
	- 修改类的静态字段
	- 修改类中定义的方法

###### 三种姿势结合举例介绍
- 现在创建一个元类，实现每一个通过该元类创建的类的静态字段中，如果该静态字段的值为`字符串`类型，就将该字符串的值全部变成`大写`

- 第一种姿势
	- 创建类时，第一个参数是要继承的类，使用`metaclass`指定创建该类的元类

- 第二种姿势
	- 使用元类创建出一个类之后，将类直接传入到要创建类的参数列表中。相当与创建的类继承自参数列表中的类。当创建该类的时候，由于没有直接给该类指定元类，那么就会去其父类中寻找元类，其父类就是参数列表中使用`MyMetaClass`创建的类，然后执行其内的`__new__`方法

- 第三种姿势
	- 第三种姿势与第二种姿势大同小异，只不过是使用`with_metaclass`返回一个由`MyMetaClass`创建的类。


```python
# 第一种姿势 -----------------------------------------------------
class MyMetaClass(type):
    def __new__(cls, name, bases, dct):
        _fields = [[k, v] for k, v in dct.items() if not k.startswith('_')]

        for field in _fields:
            if isinstance(field[1], str):
                dct[field[0]] = field[1].upper()
        return super().__new__(cls, name, bases, dct)


class Par(object):
    par_description = 'this is Par'


class Sub(Par, metaclass=MyMetaClass):
    sub_description = 'this is Sub'


sub_obj = Sub()

print(sub_obj.sub_description)				# THIS IS SUB
print(sub_obj.par_description)				# this is Par


# 第二种姿势 -----------------------------------------------------
class MyMetaClass(type):
    def __new__(cls, name, bases, dct):
        _fields = [[k, v] for k, v in dct.items() if not k.startswith('_')]

        for field in _fields:
            if isinstance(field[1], str):
                dct[field[0]] = field[1].upper()
        return super().__new__(cls, name, bases, dct)


class Par(object):
    par_description = 'this is Par'


class Sub(MyMetaClass('MyMetaClass', (Par, ), {})):
    sub_description = 'this is Sub'


sub_obj = Sub()

print(sub_obj.sub_description)
print(sub_obj.par_description)


# 第三种姿势 -----------------------------------------------------------
class MyMetaClass(type):
    def __new__(cls, name, bases, dct):
        _fields = [[k, v] for k, v in dct.items() if not k.startswith('_')]

        for field in _fields:
            if isinstance(field[1], str):
                dct[field[0]] = field[1].upper()
        return super().__new__(cls, name, bases, dct)


class Par(object):
    par_description = 'this is Par'

def with_meta(meta, par):
    return meta('MyMetaClass', (par, ), {})

class Sub(with_meta(MyMetaClass, Par)):
    sub_description = 'this is Sub'


sub_obj = Sub()

print(sub_obj.sub_description)
print(sub_obj.par_description)
```






























