# 单例模式
- 当我们想让某一个类只有一个实例的时候，我们就需要使用单例模式来设计我们的类

## 介绍
- 单例模式就像他的名字一样，该实例对应的类有且只有一个实例，这种设计模式非常常见，在python和开发中我们经常遇到
	1. None，python中一切皆对象，None对象对应的类就只有None这一个实例，因此在python中所有指向None对象的变量的id都是相同的
	2. Django的admin后台管理中，admin.py中调用的register方法注册模型类的AdminSite对象就是以模块加载来实现的单例。
	3. 开发后台管理组件时，参考的Django admin的源码，因此也用到了这种设计模式


#### 何时适合使用单例模式？
- 当我们的项目跑起来之后，在之后程序的其他代码部分永远只需要一份该对象的时候，这样一来，封装到对象中的属性就可以在程序的其他模块中通过调用对象来获取属性封装的数据

#### 如何实现单例模式
1. 使用全局变量实现
2. 使用`__new__`构造方法实现
3. 全局变量 + 类
	- 实际上利用了`python模块导入的特性`

#### 实现单例模式时要注意什么情况？
- 并发的情况下，比如多线程，需要使用锁。
- 为什么需要加锁？
	- 多线程中，如果类在实例化时`__init__`文件中存在了`IO`阻塞或者执行时间过长，那么就会产生多个实例

## 具体实现

#### 0. 使用全局变量
- 将对象的`实例化/获取`在全局命名空间中实现，这样也可以实现单例模式
- 缺点
	- 较差的扩展性，无法实现功能的扩展，如果用类的话可以解决这个问题

###### 应用一：redis链接
- CRM项目中，为了实现自动对销售顾问分配客户资源（即自动分配），会将销售顾问按照各自的`权重`和`可分配数量`组装成一个列表，将这个列表存放在`redis`服务器中，这样一方面解决了多进程情况下，如果不使用redis存储列表，多个进程同时出现程序错误时，回滚到`rollback_list`中的`销售顾问id`顺序会出现错误

```python
import redis
from collections import OrderedDict
from django.conf import settings

from crm import models

CONN = redis.Redis(
    host=settings.REDIS_HOST,
    port=settings.REDIS_PORT,
    password=settings.REDIS_PASSWORD,
)


class HandlerDistribution(object):
    """ 实现客户资源分配
    功能:
        生成权重列表
    """

    salers = None
    salers_iter = None
    need_reset = False
    rollback_list = []

    @classmethod
    def fetchall(cls):
        """ 获取数据并组装成权重列表
        Return:
            权重列表，包含的是一个个销售记录对象
        """

        distributions = models.Distribution.objects.all().order_by('-weight')

        fetch_dict = OrderedDict()
        for distribution in distributions:
            fetch_dict[distribution.user] = distribution.num

        result = []
        max_range = max(fetch_dict.values())     # 获取最大循环数
        for i in range(max_range):
            for user, count in fetch_dict.items():
                if count>0:
                    result.append(user.id)
                    fetch_dict[user] -= 1

        if result:
            CONN.rpush(settings.SALE_ID_LIST, *result)
            CONN.rpush(settings.SALE_ID_LIST_ORIGIN, *result)           # [35, 34, 33, 34, 33, 33]
            return True
        return False

    @classmethod
    def get_saler_id(cls):
        """ 迭代salers_iter
        Return:
            返回值为销售人员的id
        """

        sale_id_origin_count = CONN.llen(settings.SALE_ID_LIST_ORIGIN)
        if not sale_id_origin_count:
            status = cls.fetchall()
            if not status:              # 数据库为空
                return None

        saler_id = CONN.lpop(settings.SALE_ID_LIST)
        if saler_id:                    # sale_id存在
            return saler_id

        # sale_id不存在，说明sale_id_list为空
        reset = CONN.get(settings.SALE_ID_RESET)
        if reset:                       # 需要重置，刷新sale_id_list_origin
            CONN.delete(settings.SALE_ID_LIST_ORIGIN)
            status = cls.fetchall()
            if not status:
                return None
            CONN.delete(settings.SALE_ID_RESET)
            return CONN.lpop(settings.SALE_ID_LIST)
        else:                           # 不需要重置，将sale_id_list_origin中数据保存一份到sale_id_list中
            sale_id_list_origin_count = CONN.llen(settings.SALE_ID_LIST_ORIGIN)
            for i in sale_id_list_origin_count:
                value = CONN.lindex(settings.SALE_ID_LIST_ORIGIN, i)
                CONN.rpush(settings.SALE_ID_LIST, value)
            return CONN.lpop(settings.SALE_ID_LIST)


    @classmethod
    def reset(cls):
        """ 当分配表中的记录变化时，此方法将会重置静态字段salers的值
        Return:
            None
        """

        CONN.set(settings.SALE_ID_RESET, 1)

    @classmethod
    def rollback(cls, sale_id):
        CONN.lpush(settings.SLAE_ID_LIST,sale_id)
```




###### 应用二：pymysql操作数据库
- 之前使用`pymysql`设计一个封装了`增、删、改、查`的模块时，就应用到了这种单例模式。如果你不使用单例模式，每一次对数据库的操作可能都会重复建立连接这个步骤，这样显然是有问题的，我们要避免不必要的重复链接，因为每一次的数据库连接都会产生性能损耗
- 小项目结构介绍
	1. 配置文件`config.ini`用来存放链接数据库的配置信息
	2. `curd.py`用来存放`增、删、改、查`功能的类以及实现`链接`的`单例模式`

```ini
# config.ini
[mysql]
host = 172.96.203.6
port = 3306
database = test
user = haha
password = passhaha
charset = utf8
```



```python
# curd.py
import pymysql
import configparser


def get_config():
    parser = configparser.ConfigParser()
    parser.read(filenames='config.ini', encoding='utf-8')
    config = dict(parser['mysql'])
    config['port'] = int(config['port'])
    return config


connection = pymysql.connect(**get_config())


class MysqlCURD(object):
    def __init__(self):
        self.connection = connection
```

```python
from curd import MysqlCURD


a = MysqlCURD()
b = MysqlCURD()

print(id(a))        # 49984464
print(id(b))        # 49984592
print(id(a.connection))  # 53963184
print(id(b.connection))  # 53963184	，证明实现了单例模式
```

#### 1. python通过文件加载实现单例模式
- python导入模块有一个机制，对于同一个模块，只是我们使用`import`导入了多次这个模块，python也只会将该模块加载一次到内存，因此我们完全可以在模块中预先实例化好一个对象，当我们在别处导入改模块地时候，不管导入多少次，只要内存种已经存在了这个对象，那么在其他多个文件中导入这个模块得到的对象都会是同一个。

###### 应用一：seconds组件

- 在开发后台管理组件`seconds`的过程中，参考了Django admin后台管理的源码，发现在其中的`admin.site`对象就是利用这种方式是实现的单例模式，下面放源码


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

#### 2. 使用类的构造函数"__new__"
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

#### 3. 使用装饰器
###### 1. 举一个骚气冲天的例子


```python
class Foo(object):
    instance_lock = Lock()

    def __init__(self, cls):
        self._cls = cls
        self._instances = None

    def __call__(self, *args):
        with Foo.instance_lock:
            if not self._instances:
                self._instances = self._cls(*args)
        return self._instances


@Foo
class A(object):
    def __init__(self):
        time.sleep(1)
```

#### 4. 使用静态字段
> Tornado内部单例模式就是使用这种方式实现
- 这种方法，在多线程下会出问题，会出现多个实例

```python
class Foo:
	_instance = None
	
	@classmethod
	def instance(self):
		if not Foo._instance:
			obj = Foo(*args, **kwargs)		
			Foo._instance = obj
		return  Foo._instance
```

- 解决方案：加锁

```python
import time
import threading
class Foo(object):
    _instance_lock = threading.Lock()

    def __init__(self):
        time.sleep(1)

    @classmethod
    def instance(cls, *args, **kwargs):
        if not hasattr(Foo, "_instance"):		# 当单个线程的时候，没有必要加锁
            with Foo._instance_lock:
                if not hasattr(Foo, "_instance"):
                    Foo._instance = Foo(*args, **kwargs)
        return Foo._instance
```


#### 5. 使用元类

```python
from threading import Lock


class Single(type):
    single_lock = Lock()

    def __call__(self, *args, **kwargs):
        if not hasattr(self, '_instance'):
            with Single.single_lock:
                if not hasattr(self, '_instance'):
                    self._instance = super(Single, self).__call__(*args, **kwargs)
        return self._instance


class Foo(object, metaclass=Single):
    def __init__(self):
        pass
```

