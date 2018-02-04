# 使用数据库连接池(DBUtils源码分析)

#### 为什么要使用`数据库连接池`
###### 小白版
- 现在假设在Web开发中，某开发小白需要使用`pymysql(3)/mysqldb(2)`来链接数据库，他将连接数据库的操作放在的视图函数中，然后通过创建的`connection`对象操作数据库，在模板中渲染这些数据。代码简单写成这样（以Django为例）

```python
def show_view(request):
    connection = pymysql.connect(       # 其实这些参数值应该保存在配置文件中
            host='xxx', 
            user='xxx', 
            password='xxx', 
            database='test',
    )
    cursor = connection.cursor()
    # 此处省略n行业务代码
    cursor.close()
    connection.close()
    return render('xxx.html', {"data": data})
```

- 这样写是要出大事儿的，一定不能将数据库创建链接的操作放在视图函数中。开发小白这样写意味着，每一次用户向该页面发起请求，都会连接一次数据库，对于流量非常大的Web站点来说，这样做服务器根本无法承受，估计直接就崩了。

###### 小白进阶版
- 开发小白现在涨姿势了，知道不能把创建链接的操作放在视图函数中，于是他在全局变量中创建了一个`connection`对象。

```python
connection = pymysql.connect(
        host='xxx', 
        user='xxx', 
        password='xxx', 
        database='test',
)

def show_view(request):
    cursor = connection.cursor()
    # 此处省略n行业务代码
    cursor.close()
    return render('xxx.html', {"data": data})
```

- 这样写也会出大事儿的，因为大量用户访问该页面的时候会共用同一个`connection`链接

####### 小白再进阶版
- 小白现在更“机智”了，他打算对每一个`connection`对象加上“锁”，这样就不会出现多个用户请求对`connection`链接出现`线程不安全`的情况

```python
import pymysql
import threading
from threading import RLock

LOCK = RLock()

connection = pymysql.connect(
        host='xxx', 
        user='xxx', 
        password='xxx', 
        database='test',
)


def show_view(request):
    with LOCK:
        cursor = connection.cursor()
        # 此处省略n行业务代码
        cursor.close()
        return render('xxx.html', {"data": data})
 
```

- 然而这样还是存在问题，`Rlock`本质上就是一个`互斥锁`，`互斥锁`会让任务的并发执行变成串行执行，这意味着大量用户只能排队等待，显然也要粗大事儿


###### 上述情况分析
- 第一种情况中，对于多线程来说，每一个线程中对页面的请求都会创建一个链接，虽然实现了并发，但是服务器受不了
- 第三种情况，虽然保证了一个`connection`同一时刻只能被同一个线程使用，但是强制将多线程并发变成串行


## 数据库连接池详细介绍

#### 模式一
- 模式一**基于threading.Local实现，他保证每一个线程都有自己独立的链接，该连接不会被其他线程使用**
- 特点
    1. 为每一个线程都创建一个`connection`链接，并且该`connection`对于每一个线程而言都是`"私有的"(local本地线程)`
    2. 每一个线程在使用`DBUtils`提供的`conn.close`时并不是真正的关闭线程与数据库的链接，而是将`connection`放回`连接池`，当线程再次需要使用该`connection`时，会再次从`连接池`中取出该连接（避免`connection`的重复建立）
        - 这是因为`DButils`创建的conn并不是pymysql与数据库创建的真正的conn，而是自己封装的`SteadyDBConnection`
    3. 当线程被终止时，与数据库建立的链接才会被真正断开


###### 使用
- 参数
    - `creator`：指定使用链接数据库的模块，如果你是python3，就是pymysql，如果是python2，就是mysqldb
    - `maxusage`：一个链接最多被重复使用的次数，None表示无限制，当链接达到做最多使用次数时，就会关闭该连接并创建一个新的链接替换旧链接
    - `setsession`：开始会话前要在数据库中执行的命令列表，常用来修改数据库会话配置。如：["set datestyle to ...", "set time zone ..."]
    - `ping`：用于检测服务端链接
        - `0 = None = never`
        - `1 = default = whenever it is requested`
        - `2 = when a cursor is created`
        - `4 = when a query is executed`
        - `7 = always`
    - `closeable`：通常设置为False，此时使用`conn.close`就不会真正关闭`connection`，而是放回连接池，如果设置为True，就会在执行`conn.close`的时候关闭与数据库的`connection`链接，该线程下次再链接时（调用pool.connection）就会报错，因为已经真正的关闭了链接
    - `threadlocal`：本线程独享的对象，用于保存链接对象，如果链接对象被重置
    - `host, port, user, password, database, charset等`：用于使用`mysqldb/pymysql`创建`connection`对象

```python
POOL = PersistentDB(
    creator=pymysql,      
    maxusage=None,        
    setsession=[],        
    ping=0,              
    closeable=False,
    threadlocal=None, 
    host='127.0.0.1',
    port=3306,
    user='root',
    password='123',
    database='pooldb',
    charset='utf8'
)

def func():
    conn = POOL.connection(shareable=False)             # 从连接池中获取该线程"私有的connection"
    cursor = conn.cursor()                              # 其他操作和mysqldb/pymysql相同
    cursor.execute('select * from tb1')
    result = cursor.fetchall()
    cursor.close()
    conn.close()                                        # 不是真正链接的关闭，而是放回数据库（当closeable=False时）
 
    conn = POOL.connection()                            # 再次从连接池获取链接，不会报错，如果`closeable=True`就会报错
    cursor = conn.cursor()
    cursor.execute('select * from tb1')
    result = cursor.fetchall()
    cursor.close()
    conn.close()

import threading

for i in range(10):
    t = threading.Thread(target=func)
    t.start()
```

###### 源码分析(注意我在注释中的解释)
1. 从实例化一个`PersistentDB`开始

```python
class PersistentDB:

    version = __version__

    def __init__(self, creator,
            maxusage=None, setsession=None, failures=None, ping=1,
            closeable=False, threadlocal=None, *args, **kwargs):
        try:
            threadsafety = creator.threadsafety                 # 获取pymysql/mysqldb中的threadsafety配置，在pymysql/mysqldb中threadsafety=1，表示链接是线程安全的
        except AttributeError:
            try:
                if not callable(creator.connect):
                    raise AttributeError
            except AttributeError:
                threadsafety = 1
            else:
                threadsafety = 0
        if not threadsafety:
            raise NotSupportedError("Database module is not thread-safe.")
        self._creator = creator
        self._maxusage = maxusage
        self._setsession = setsession
        self._failures = failures
        self._ping = ping
        self._closeable = closeable
        self._args, self._kwargs = args, kwargs
        self.thread = (threadlocal or local)()                  # 每一个线程都实现了`threading.local`，保证了链接的"private"

    def steady_connection(self):
        """创建一个steady_connection，而非mysqldb/pymysql创建的conn!"""    
        return connect(
            self._creator, self._maxusage, self._setsession,
            self._failures, self._ping, self._closeable,
            *self._args, **self._kwargs)

    def connection(self, shareable=False):
        """ 从连接池中获取一个链接，并非创建链接，创建连接使用的是 steady_connection() """
        try:
            con = self.thread.connection
        except AttributeError:
            con = self.steady_connection()
            if not con.threadsafety():
                raise NotSupportedError("Database module is not thread-safe.")
            self.thread.connection = con                         # self.thread本质上就是一个Local()本地线程对象 ，内部实现了递归锁
        con._ping_check()
        return con                                               # 返回一个steady_connection链接

2. 看看本地线程都干了什么
    - "local"类将`_loaclimpl`对象与每一个线程绑定，为每一个线程创建了一个字典用于存放"connection"对应关系，内部实现了对该字典的`增删改查`操作

class local:                                                
    __slots__ = '_local__impl', '__dict__'

    def __new__(cls, *args, **kw):
        if (args or kw) and (cls.__init__ is object.__init__):
            raise TypeError("Initialization arguments are not supported")
        self = object.__new__(cls)
        impl = _localimpl()
        impl.localargs = (args, kw)
        impl.locallock = RLock()                                # 实例化一个递归锁
        object.__setattr__(self, '_local__impl', impl)          # 把实例化得到的"impl"与当前线程绑定
        impl.create_dict()                                      # 为当前线程创建一个字典对象！！！
        return self 

    def __getattribute__(self, name):
        with _patch(self):
            return object.__getattribute__(self, name)

    def __setattr__(self, name, value):
        if name == '__dict__':
            raise AttributeError(
                "%r object attribute '__dict__' is read-only"
                % self.__class__.__name__)
        with _patch(self):
            return object.__setattr__(self, name, value)

    def __delattr__(self, name):
        if name == '__dict__':
            raise AttributeError(
                "%r object attribute '__dict__' is read-only"
                % self.__class__.__name__)
        with _patch(self):
            return object.__delattr__(self, name)

class _localimpl:
    """ 用来处理thread-local字典的类 """
    __slots__ = 'key', 'dicts', 'localargs', 'locallock', '__weakref__'

    def __init__(self): 
        self.key = '_threading_local._localimpl.' + str(id(self))    # { id(Thread) ： (ref(Thread), thread-local dict) }
        self.dicts = {}

    def get_dict(self):                                              # 获取当前线程 thread-local的字典
        thread = current_thread()
        return self.dicts[id(thread)][1]

    def create_dict(self):                                           # 为当前线程创建一个字典并返回
        localdict = {}
        key = self.key
        thread = current_thread()
        idt = id(thread)
        def local_deleted(_, key=key): 
            thread = wrthread()
            if thread is not None:
                del thread.__dict__[key]
        def thread_deleted(_, idt=idt): 
            local = wrlocal()
            if local is not None:
                dct = local.dicts.pop(idt)
        wrlocal = ref(self, local_deleted)
        wrthread = ref(thread, thread_deleted)
        thread.__dict__[key] = wrlocal
        self.dicts[idt] = wrthread, localdict
        return localdict
```

#### 模式二
- 模式二设计的思想类似`进程池/线程池`，简单的说，再次模式下，连接池中会有制定最大数量的`worker connection`类似`worker thread/process`


- 特点
    1. 在`连接池`中创建一批`connection`，为所有线程提供`connection`
    2. 线程需要使用到数据库链接时，直接来`连接池`中取一个`connection`使用，而不需要单独为该线程创建一个`connection`
    3. 存在最大连接数（即进程池中能够放置的最`connection数量`），当进程池中所有`connection`都被线程占用的时候，其他线程只能在`connection`使用完毕放回`连接池`后才能获取该`connection`
        - 这里内部其实使用的是线程的阻塞blocking
    4. `pymysql/mysqldb`的`threadsafety=1`时，连接池设置的`maxshared`参数是没有用的，因为源码中做了一个判断，只有`threadsafety>1`时`maxshared`才会有效，下面源码会介绍
      
```python
        if threadsafety > 1 and maxshared:
            self._maxshared = maxshared
            self._shared_cache = []  # the cache for shared connections
```   

###### 使用
- 参数（部分与模式一相同）
    - `maxconnections`连接池允许的最大连接数，0和None表示不限制连接数
    - `mincached`初始化时，连接池中至少要创建的链接的数量，0表示不创建
    - `maxcached`链接池中最多闲置的链接，0和None不限制
    - `maxshared`链接池中最多共享的链接数量，0和None表示全部共享。PS: 无用，因为pymysql和MySQLdb等模块的 threadsafety都为1，所有值无论设置为多少，_maxcached永远为0，所以永远是所有链接都共享。
    - `blocking`连接池中如果没有可用连接后，是否阻塞等待。True，等待；False，不等待然后报错


```python
import time
import pymysql
import threading
from DBUtils.PooledDB import PooledDB, SharedDBConnection


POOL = PooledDB(
    creator=pymysql,  
    maxconnections=6,            
    mincached=2,                 
    maxcached=5,                 
    maxshared=3,                 
    blocking=True,   
    maxusage=None,  
    setsession=[],   
    ping=0,
    host='127.0.0.1',
    port=3306,
    user='root',
    password='123',
    database='pooldb',
    charset='utf8'
)


def func():
 
    conn = POOL.connection()

    print(th, '链接被拿走了', conn1._con)
    print(th, '池子里目前有', pool._idle_cache, '\r\n')

    cursor = conn.cursor()
    cursor.execute('select * from tb1')
    result = cursor.fetchall()
    conn.close()


func()
```

###### 使用单例模式实现数据库连接池

```python
import pymysql
import threading
from DBUtils.PooledDB import PooledDB

class SingletonDBPool(object):
    _instance_lock = threading.Lock()

    def __init__(self):
        self.pool = PooledDB(
            creator=pymysql,  
            maxconnections=6,   
            mincached=2,    
            maxcached=5,   
            maxshared=3, 
            blocking=True,  
            maxusage=None,   
            setsession=[],   
            ping=0, 
            host='127.0.0.1',
            port=3306,
            user='root',
            password='123',
            database='pooldb',
            charset='utf8'
        )

    def __new__(cls, *args, **kwargs):
        if not hasattr(SingletonDBPool, "_instance"):
            with SingletonDBPool._instance_lock:
                if not hasattr(SingletonDBPool, "_instance"):
                    SingletonDBPool._instance = object.__new__(cls, *args, **kwargs)
        return SingletonDBPool._instance

    def connect(self):
        return self.pool.connection()
```

###### 源码分析
- 流程分析
    1. 检测当前正在运行连接数的是否小于最大链接数，
        1. 如果不小于则：等待或报raise TooManyConnections异常
        2. 如果存在可使用的链接，则优先去初始化时创建的链接中获取链接 SteadyDBConnection。
    2. 然后将SteadyDBConnection对象封装到PooledDedicatedDBConnection中并返回。如果最开始创建的链接没有链接，则去创建一个SteadyDBConnection对象，再封装到PooledDedicatedDBConnection中并返回，并且当前连接数+1。
    3. 一旦关闭链接后，连接就返回到连接池让后续线程继续使用，并且当前连接数-1。


- 源码

```python
1. 实例化

class PooledDB:

    version = __version__

    def __init__(self, creator,
            mincached=0, maxcached=0,
            maxshared=0, maxconnections=0, blocking=False,
            maxusage=None, setsession=None, reset=True,
            failures=None, ping=1,
            *args, **kwargs):
       
        try:
            threadsafety = creator.threadsafety
        except AttributeError:
            try:
                if not callable(creator.connect):
                    raise AttributeError
            except AttributeError:
                threadsafety = 2
            else:
                threadsafety = 0
        if not threadsafety:
            raise NotSupportedError("Database module is not thread-safe.")
        self._creator = creator
        self._args, self._kwargs = args, kwargs
        self._blocking = blocking
        self._maxusage = maxusage
        self._setsession = setsession
        self._reset = reset
        self._failures = failures
        self._ping = ping
        if mincached is None:
            mincached = 0
        if maxcached is None:
            maxcached = 0
        if maxconnections is None:
            maxconnections = 0
        if maxcached:
            if maxcached < mincached:
                maxcached = mincached
            self._maxcached = maxcached
        else:
            self._maxcached = 0
        if threadsafety > 1 and maxshared:
            self._maxshared = maxshared
            self._shared_cache = []  # the cache for shared connections
        else:
            self._maxshared = 0
        if maxconnections:
            if maxconnections < maxcached:
                maxconnections = maxcached
            if maxconnections < maxshared:
                maxconnections = maxshared
            self._maxconnections = maxconnections
        else:
            self._maxconnections = 0
        self._idle_cache = []               # 存放缓存链接的列表，mincached
        self._lock = Condition()            # threading.Condition()
        self._connections = 0 
        idle = [self.dedicated_connection() for i in range(mincached)]
        while idle:
            idle.pop().close()



    def connection(self, shareable=True):
 
        if shareable and self._maxshared:
            self._lock.acquire()
            try:
                while (not self._shared_cache and self._maxconnections
                        and self._connections >= self._maxconnections):
                    self._wait_lock()
                if len(self._shared_cache) < self._maxshared:
                    # shared cache is not full, get a dedicated connection
                    try:  # first try to get it from the idle cache
                        con = self._idle_cache.pop(0)
                    except IndexError:  # else get a fresh connection
                        con = self.steady_connection()
                    else:
                        con._ping_check()  # check this connection
                    con = SharedDBConnection(con)
                    self._connections += 1
                else:  # shared cache full or no more connections allowed
                    self._shared_cache.sort()  # least shared connection first
                    con = self._shared_cache.pop(0)  # get it
                    while con.con._transaction:
                        # do not share connections which are in a transaction
                        self._shared_cache.insert(0, con)
                        self._wait_lock()
                        self._shared_cache.sort()
                        con = self._shared_cache.pop(0)
                    con.con._ping_check()  # check the underlying connection
                    con.share()  # increase share of this connection
                # put the connection (back) into the shared cache
                self._shared_cache.append(con)
                self._lock.notify()
            finally:
                self._lock.release()
            con = PooledSharedDBConnection(self, con)
        else:                                                           # 获取一个 dedicated connection 并返回
            self._lock.acquire()
            try:
                while (self._maxconnections
                        and self._connections >= self._maxconnections):     # 先判断当前连接数是否大于最大连接数
                    self._wait_lock() 
                try:                                                        # 首先从初始化时创建的connection中获取链接
                    con = self._idle_cache.pop(0)
                except IndexError:                                          # 否则就实例化一个steady_connection返回
                    con = self.steady_connection()
                else:
                    con._ping_check()                                       # 检测链接
                con = PooledDedicatedDBConnection(self, con)
                self._connections += 1                                      # 当前连接数+1
            finally:            
                self._lock.release()                                        # 释放condition锁
        return con


    def close(self):
        
        self._lock.acquire()
        try:
            while self._idle_cache:                                         # 关闭_idle_cache中所有链接
                con = self._idle_cache.pop(0)
                try:
                    con.close()
                except Exception:
                    pass
            if self._maxshared:                                             # 关闭所有共享的链接
                while self._shared_cache:
                    con = self._shared_cache.pop(0).con
                    try:
                        con.close()
                    except Exception:
                        pass
                    self._connections -= 1                                  # 当前连接数-1
            self._lock.notifyAll()
        finally:
            self._lock.release()
```