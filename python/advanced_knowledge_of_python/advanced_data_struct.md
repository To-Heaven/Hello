## 常量
- 变量需要注意的地方
	- 不可变性
	- 常量名应该大写
- 但是我们在python中定义的常量（本质上是一个变量）总是可以更改的，在程序运行的过程中绝对不允许常量被改变，除非你定义的是一个变量，因此需要我们通过类去实现一个满足常量上述条件的对象
- 代码如下

```python
# constant.py
import sys

class const:
    ''' 用于创建常量对象，并保证常量对象的不可变性和命名规范 '''

    class ConstError(TypeError):pass            # 用于在修改常量操作时触发错误
    class ConstCaseError(ConstError):pass       # 用于在定义不规范常量名时触发错误

    def __setattr__(self, name, value):
        ''' 创建常量
        Args：
            name: string，常量的名称，如果不是全部大写会触发ConstCaseError错误，
                                    如果常量已存在会触发ConstError错误
            value: arbitrary type，常量的值，如果
        Return:
            None
        '''
        if self.__dict__.get(name):
            raise self.ConstError("Can't change const.{name}".format(name=name))
        if not name.isupper():
            raise self.ConstCaseError("const name {name} is not all uppercase".format(name=name))
        self.__dict__[name] = value

sys.modules[__name__] = const()                 # 导入此模块，直接使用模块名创建常量
# ---------------------------------------------------------------
import constant

constant.TOTAL = 1000
constant.CHUNK_SIZE = 100
# ---------------------------------------------------------------
import constant as file

file.TOTAL = 1000
file.CHUNK_SIZE = 100
``` 

- [使用枚举enumeration创建变量](http://www.ziawang.com/python/basic_knowledge_of_python/enum.html)


# python2与python3中的type()
- 在python3中，没有了古典类，创建的类都默认继承自object类。但是在python2中创建的类默认是古典类。	
	- 所谓古典类即创建的类不继承自任何类
	- 所谓新式类就是创建的类继承自object类

>  那么问题来了

- 在python2中，对古典类实例化得到的对象使用type()判断其类型，得到的结果全部是一样的，即使实例化的对象来自不同的古典类

```python
Python 2.7.13 (v2.7.13:a06454b1afa1, Dec 17 2016, 20:53:40) [MSC v.1500 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> class A:
...     pass
...
>>> class B:
...     pass
...
>>> a = A()
>>> b = B()
>>> type(a)
<type 'instance'>
>>> type(b)
<type 'instance'>
>>> isinstance(a, A)
True
>>> isinstance(b, B)
True
>>> type(a) == type(b)
True
>>>
```

- 在这种情况下，type()函数明显就没有用处，在某些情况可能会让程序不走理想的逻辑流程。
- type()缺点
	- 对于用户基于内建类扩展的自定义类，type并不能准确返回结果
	- python2中type()对所有古典类实例化对象返回的结果全都相同，即无效。（对python2中的新式类有效）
- 解决这种情况的方法
	-  `isinstance(obj, classinfo)`


## None 与0、空值对象False
- None是空值对象，是NoneType类的实例（单例模式）


```python
>>> a = None
>>> b = None
>>> id(None)
1515193552
>>> id(a)
1515193552
>>> id(b)
1515193552
>>>
```

- python中数据为空的对象
	- None
	- False
	- 任何数值形式值为0的对象：0、0.0、0j
	- 空序列
	- 空字典
	- **用户定义的类中如果存在`__nonzero__()`和`__len__()方法`**
		- 如果`__nozero__()`返回值的布尔值为False
		- 如果`__len__()`返回值的布尔值为False


```python


```


- 注意
	- **所有赋值为None的对象都相等，None对象与任何其他非None的对象比较的结果都为False**


## 再说可变对象与不可变对象
- 不可变对象
	- 不可变对象其中一个优点就是：当两个引用同时指向内存中的同一个不可变对象的时候，每个根据引用对该不可变对象的操作都是相互隔离的，即不会产生相互的影响。
- 可变对象
	- 可变对象在很多情况下不能作为函数参数的默认值。
	- 可变对象作为默认参数时的应用场景
		- 但是当可变对象作为递归函数的默认参数时，往往很有效



```
# 编写一个函数，从下面嵌套字典中返回由指定的字段和对应的值组成的字典。如果指定字段不存在，则跳过该字段
# fields:由"|"连接的以"fld"开头的字符串,如:fld2|fld3|fld7|fld19
data = {"time ": "2016-08-05T13:13:05 ",
        "some_id ": "I D1234 ",
        "fld7": {"fld5": 20},
        "grp1": {"fld1": 1, "fld2": 2},
        "xxx2": {"fld3": 0, "fld5": 0.4},
        "fld6": 11,
        "fld46": 8}

fields = "fld2|fld3|fld7|fld19"

result = {}


def select(data, fields):
    list_fields = fields.split('|')
    for key in data:
        if key in list_fields:
            result[key] = data[key]
        if type(data[key]) is dict:
            res = select(data[key], fields)
            result.update(res)
    return result


dict_select = select(data, fields)
print(dict_select)

# -----------------------------------------------------------------
list_fields = fields.split('|')


def select(data, list_fields, result={}):
    for key in data:
        if key in list_fields:
            result[key] = data[key]
        if type(data[key]) is dict:
            res = select(data[key], list_fields)
            result.update(res)
    return result


dict_select = select(data, list_fields)
print(dict_select)
```