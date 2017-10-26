# python2与python3中的type()
- 在python3中，没有了古典类，创建的类都默认继承自object类。但是在python2中创建的类默认是古典类。	
	- 所谓古典类即创建的类不继承自任何类
	- 所谓新式类就是创建的类继承自object类

>  那么问题来了

- 在python2中，对古典类实例化得到的对象使用type()判断其类型，得到的结果全部是一样的

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



## 再说新式类和古典类
- `type(obj)`和`obj.__class__`
	- 两者都是查看obj对象的类，只不过一个是内建函数，一个是对象属性
	- 对于python2中的古典类，两者的结果是不同的

- 在python2的古典类中，`type(obj)`返回的是`instance`，通过对象查看其类需要使用`obj.__class__`

- python2中判断一个类是古典类还是新式类通常通过其基类判断，继承自object类的类就是新式类，但是简单的通过查看创建类时的参数列表中是否有object并不准确。影响类是新式类还是古典类的因素还有一个，就是`__metaclass__`
	- python2如果创建类时，参数列表为空
		-  `__metaclass__`设置为`type`类，那么这个类就是新式类
		-  `__metaclass__`设置为`types.ClassType`类，这个类就是古典类
			-  如果参数列表中声明了继承自`object`类，那么解释器会忽略`__metaclass__`设置

```python
class A:
    pass													# 古典类


class B(object):
    pass													# 新式类


a = A()
b = B()

print('type(a):', type(a), 'a.__class__:', a.__class__)
print('type(b):', type(b), 'b.__class__:', b.__class__)


class C:
    __metaclass__ = type									# 新式类


class D:
    import types
    __metaclass__ = types.ClassType							# 古典类


c = C()
d = D()
print('type(c):', type(c), 'c.__class__:', c.__class__)
print('type(d):', type(d), 'd.__class__:', d.__class__)

```

- 注意
	- type()与对象属性__class__
		- 古典类实例化的对象，type()返回的是instance，与属性__class__返回的结果不同
		- 新式类实例化的对象，type()与__class__属性返回的结果是一样的
	- 基类
		- 古典类没有基类
		- object类也没有基类
		- type类的基类是object
	- 继承
		- 继承自内建类型的类也是新式类
> 古典类中，所有用户定义的类的类型都是instance（即type的结果都是instance）

