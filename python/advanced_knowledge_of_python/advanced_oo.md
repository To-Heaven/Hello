
## 静态方法与类方法
- 两者都是通过装饰器来实现，并且既可以被类名调用，又可以被实例调用
- staticmethod不需要传入类或者实例对象作为隐式参数，classmethod需要提供`cls`代表类作为隐式参数传入
- 当实例对象调用类方法的时候，`cls`参数代表该对象所在的类，如果classmethod时继承自父类，那么子类实例调用该classmethod的时候，cls代表的是子类而不是父类
- 静态方法在调用的过程中不需要传入`cls`和`self`参数，即调用过程与类和实例都不相关，但是静态方法存在的意义就在于，我们把与对象逻辑业务上相关的方法作为静态方法定义在类中，可以增强代码的可维护性（其实也可以将静态方法风状态另一个与其相关的类中）。

## 构造函数与初始化函数
- 实例化类创建对象的时候，会调用类中的两个方法，`__new__(cls, *args, **kwargs)`和`__init__(self)`
	- `__new__(cls, *args, **kwargs)`是构造函数，是一个类方法。对象由这个构造方法产生
	- `__init__(self)`是初始化函数，是一个对象方法。对象通过这个方法实现初始化，比如绑定属性，进行其他逻辑运算等

- 两者的关系
	- 实例化类时，`__new__()`先于`__init__()`执行，并且`__new__()`方法会显式返回一个对象，同时会调用对象的`__init__()`方法完成初始化。因此，如果构造函数没有返回一个对象，那么就不会触发调用对象的`__init__`方法，因为根本就不存在该类的对象(None)，更不可能调用方法了
	- `__new__(cls, args)`与`__init__(self, args)`函数定义时的参数必须要一致，也就是说两者的`args`参数形式要一致，名称可以不同
	- `__new__(cls, args)`返回的类对象（不是实例对象）
		- 待补充

- 构造方法
	- 大多数情况下不需要我们覆写`__new__()`方法，但是当我们使用默认继承来的构造函数不能构造出我们需要的对象的时候，特别时继承自不可变类型的时候，需要我们覆写父类的`__new__`方法
	- 使用工厂模式、单利模式以及用元类进行编程的时候
	- 假如现在需要构建一个frozenset的子类，实现对字符串中单词的获取




```python
# 使用默认继承的__new__
class UserSet(frozenset):
    def __init__(self, arg=None):
        if isinstance(arg, str):
            arg = arg.split()
        super().__init__()


print(UserSet('ziawang  hahaha'))
print(frozenset('ziawang  hahaha'))
```

```python
# 自定义__new__

class UserSet(frozenset):

    def __new__(cls, *args):
        if args[0] and isinstance(args[0], str):
            args = (args[0].split(), ) + args[1:]
        return super().__new__(cls, *args)

    def __init__(self, *args):
        super().__init__()

print(UserSet('ziawang hahaha hahaah '))
print(frozenset('ziawang haha ahaa '))
```


