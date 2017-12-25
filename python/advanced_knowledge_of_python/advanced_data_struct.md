## 常量

#### 常量的特点
1. 不可变性
2. 常量名应该大写


#### 创建常量类
- 但是我们在python中定义的常量（本质上是一个变量）总是可以更改的，在程序运行的过程中绝对不允许常量被改变，除非你定义的是一个变量，因此需要我们通过类去实现一个满足常量上述条件的对象
- 代码如下

```python
# constant.py

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

const = Const()				# 单例模式
# ---------------------------------------------------------------
from constant import const

const.TOTAL = 1000
const.CHUNK_SIZE = 100
# ---------------------------------------------------------------
from constant import const as file

file.TOTAL = 1000
file.CHUNK_SIZE = 100
``` 

- [使用枚举enumeration创建变量](http://www.ziawang.com/python/basic_knowledge_of_python/enum.html)


# python2与python3中的type()
- 在python3中，没有了古典类，创建的类都默认继承自object类。但是在python2中创建的类默认是古典类。	
	- 所谓古典类即不继承自任何类的类
	- 所谓新式类就是继承自object类的类

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
		- 解决这种情况的方法 `isinstance(obj, classinfo)`

## type与isinstance
#### isinstance
- 判断一个对象是否是一个类或该类基类(包括"祖先类")的实例。
	- 注意：python2中的新式类type判断对象类型是有效的，但是在古典类中无效

```python
Python 3.6.1 (v3.6.1:69c0db5, Mar 21 2017, 17:54:52) [MSC v.1900 32 bit (Intel)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> class Foo(object):
...     pass
...
>>> class Sub(Foo):
...     pass
...
>>> a = Sub()
>>> isinstance(a, Sub)
True
>>> isinstance(a, Foo)
True
>>> type(a) == Foo
False
>>> type(a) == Sub
True
>>>

# --------------------- python2 ----------------------
Python 2.7.13 (v2.7.13:a06454b1afa1, Dec 17 2016, 20:42:59) [MSC v.1500 32 bit (Intel)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> class Foo(object):
...     pass
...
>>> class Sub(Foo):                        # python3新式类 
...     pass
...
>>> b = Sub()
>>> isinstance(b, Sub)
True
>>> isinstance(b, Foo)
True
>>> type(b) == Foo
False
>>> type(b) == Sub
True
>>> class Moo:                             # python2古典类
...     pass
...
>>> class Noo(Moo):
...     pass
...
>>> n = Noo()
>>> isinstance(n, Noo)
True
>>> isinstance(n, Moo)
True
>>> type(n) == Noo
False
>>> type(n) == Moo
False

```

#### "精确度"比较
- 对于新式类来说，isinstance的精确度显然没有type高，这也导致新式类中两者的用途不同(**python3中不再存在古典类**)
	- `isinstance`检测的是一个范围，而不是特定的某一个类型
	- `type`检测的是一个特定的类型

#### 应用
- 我在开发自己的开源插件`seconds`的过程中碰巧需要判断一个ModelForm类中字段的类型是否是`ModelChoiceField`或者`ModelMultipleChoice`，如果判断为`True`，就针对该字段展开其他业务逻辑。而恰巧后者继承自前者，因此判断的条件变成了一个范围，利用该继承关系，就可以使用`isinstance`来判断
	- 代码如下	

```python
from django.template import Library
from django.urls import reverse
from django.forms.models import ModelChoiceField

register = Library()


@register.inclusion_tag(filename='curd/form.html')
def form_popup(form_obj):
    """ 渲染表单，并为单选框/复选框后面添加popup链接按钮
    Args:
        form_obj: 视图函数响应上下文中的form对象
    """
    
    new_form = []
    for bound_field in form_obj:  
        temp = {"is_popup": False, "bound_field": bound_field}
        
        if isinstance(bound_field.field, ModelChoiceField):
            field_related_model_class = bound_field.field.queryset.model
            app_model = (
                field_related_model_class._meta.app_label, 
                field_related_model_class._meta.model_name
            )
            base_url = reverse("curd:%s_%s_add" % app_model)
            popup_url = "%s?_popbackid=%s" % (base_url, bound_field.auto_id)
            temp["is_popup"] = True
            temp["popup_url"] = popup_url
        new_form.append(temp)
        
    return {"form": new_form}

```


## None 与0、空值对象False
- None是空值对象，是NoneType类的实例（单例模式）
	- **对于一段运行着的程序，其中的所有对None的引用都指向同一个None对象**


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

- python中数据布尔值为False的对象
	- None
	- False
	- 任何数值形式值为0的对象：0、0.0、0j
	- 空序列
	- 空字典
	- **用户定义的类中如果存在`__nonzero__()`和`__len__()方法`**
		- 如果`__nozero__()`返回值的布尔值为False(python3 没有__nozero__())
		- 如果`__len__()`返回值的布尔值为0
	- **容易错的地方: 空格字符串不是False**


```python

class NoneTest1:
    def __len__(self):
        return 1


class NoneTest2:

    def __len__(self):
        return 0


print('NoneTest1', bool(NoneTest1()))
print('NoneTest2', bool(NoneTest2()))
```


- 注意
	- **所有赋值为None的对象都相等，None对象与任何其他非None的对象比较的结果都为False**


## 再说可变对象与不可变对象
- 不可变对象
	- 不可变对象其中一个优点就是：**当两个引用同时指向内存中的同一个不可变对象的时候，每个根据引用对该不可变对象的操作都是相互隔离的，即不会产生相互的影响**。当一个引用调用了该字符串类似`replace`这种方法的时候，会返回一个新的字符串，并不会对原先的字符串产生任何影响
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
