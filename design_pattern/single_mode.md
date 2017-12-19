# 单例模式
- 当我们想让某一个类只有一个实例的时候，我们就需要使用单例模式来设计我们的类

## 介绍
- 单例模式就像他的名字一样，该实例对应的类有且只有一个实例，这种设计模式非常常见，在python和开发中我们经常遇到
	1. None，python中一切皆对象，None对象对应的类就只有None这一个实例，因此在python中所有指向None对象的变量的id都是相同的
	2. Django的admin后台管理中，admin.py中调用的register方法注册模型类的AdminSite对象就是以模块加载来实现的单例。
	3. 开发后台管理组件时，参考的Django admin的源码，因此也用到了这种设计模式






#### 1. python通过文件加载实现单例模式
- python导入模块有一个机制，对于同一个模块，只是我们使用`import`导入了多次这个模块，python也只会将该模块加载一次到内存，因此我们完全可以在模块中预先实例化好一个对象，当我们在别处导入改模块地时候，不管导入多少次，只要内存种已经存在了这个对象，那么在其他多个文件中导入这个模块得到的对象都会是同一个。

###### 应用一：Django admin

- 在开发后台管理组件项目的过程中，参考了Django admin后台管理的源码，发现在其中的`admin.site`对象就是利用这种方式是实现的单例模式，下面放源码


```python
# sites.py
class AdminSite:
    """
    An AdminSite object encapsulates an instance of the Django admin application, ready
    to be hooked in to your URLconf. Models are registered with the AdminSite using the
    register() method, and the get_urls() method can then be used to access Django view
    functions that present a full admin interface for the collection of registered
    models.
    """

    # Text to put at the end of each page's <title>.
    site_title = gettext_lazy('Django site admin')

    # Text to put in each page's <h1>.
    site_header = gettext_lazy('Django administration')

    # Text to put at the top of the admin index page.
    index_title = gettext_lazy('Site administration')

    # URL for the "View site" link at the top of each admin page.
    site_url = '/'

    _empty_value_display = '-'

    login_form = None
    index_template = None
    app_index_template = None
    login_template = None
    logout_template = None
    password_change_template = None
    password_change_done_template = None

    def __init__(self, name='admin'):
        self._registry = {}  # model_class class -> admin_class instance
        self.name = name
        self._actions = {'delete_selected': actions.delete_selected}
        self._global_actions = self._actions.copy()
        all_sites.add(self)
	
	# 	此处省略部分

# This global object represents the default admin site, for the common case.
# You can instantiate AdminSite in your own code to create a custom admin site.
site = AdminSite()
```

- 在其他文件中使用site对象

```python
# urls.py

from django.contrib import admin

urlpatterns = [
    path('admin/', admin.site.urls),
]
```

```python
# admin.py

from django.contrib import admin

from room import models


admin.site.register(models.MeetingRoom)
admin.site.register(models.User)
admin.site.register(models.Order)
```

###### 应用二：自定义常量类
- 常量有两个特征
	1. 常量名大写
	2. 常量的指在程序运行中不能被改变
- python没有enum枚举模块之前，要实现一个常量，需要我们自定义一个常量类，代码如下

```python
# constant.py
import sys

class Const:
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


const = Const()		# 单例模式
```

- 使用

```python
from constant import const

# const.a = 1			# constant.ConstCaseError: const name a is not all uppercase

const.A = 1

const.A = 10			# constant.ConstError: Can't change const.A

```

#### 使用类的构造函数"__new__"
> 再次强调！"__init__()"是初始化函数而不是构造函数！

- 我们从逻辑上来想一下单例的实现：
	- 假设现在要实例化类Foo类的一个实例
		- 如果这个类当前尚未存在实力化的对象，那么我们就允许构造函数创建一个实例对象
		- 如果这个类当前已经存在了一个实例对象，那么我们就让构造函数返回当前已经存在了的实例对象

- 下面是对上面逻辑的实现
	- 列出两段代码，第一种阅读性更好些，而且并没有影响到性能，另外考虑到单例模式最常遇到的情况肯定是该实例已经存在，因此将判断实例已存在的条件放在最前面，利用了判断的惰性。**在任何时候，我们都应该在不影响性能的情况下提高代码的可阅读性**
	- 第二种，看起来代码更简短些，看个人乐，我建议使用第一种，理由如上。
	
```python
# 第一种
class Foo:
    _current_instance = None

    def __new__(cls, *args, **kwargs):
        if cls._current_instance:
            return cls._current_instance
        else:
            cls._current_instance = super().__new__(cls)
            return cls._current_instance
```

```python
# 第二种
class Foo:
    _current_instance = None

    def __new__(cls, *args, **kwargs):
        if not cls._current_instance:
            cls._current_instance = super().__new__(cls)
        return cls._current_instance
```

#### 使用装饰器
###### 1. 举一个骚气冲天的例子
- 注意，Foo类中需要定义一个`__call__`方法，即使他什么都不干，如果你不定义，在装饰器函数中调用时会找不到该方法而报错，网上给出的大多数例子均没有定义该方法，难道他们copy的时候都没有敲一下试试对不对？

```python
def singleton(cls):
    instance = cls()
    instance.__call__ = lambda: instance
    return instance

@singleton
class Foo:
    def __call__(self, *args, **kwargs):
        pass

f = Foo()
a = Foo()

print(a is f)
print(id(a), id(f))
```

#### 使用静态字段
- 这种方法，在多线程下会出问题，会出现多个实例，

```
class Foo:
	_instance = None
	
	@classmethod
	def instance(self):
		if not Foo._instance:
			obj = Foo(*args, **kwargs)		
			Foo._instance = obj
		return  Foo._instance
```

- 加锁才行

```

```