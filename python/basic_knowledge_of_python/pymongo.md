# pymongo模块
- 类似pymysql，pymongo是python提供的可以用来与mongodb服务器进行交互的模块

## 创建链接
- python中一切皆对象，我们要创建的一个链接本质上也是一个对象，因此在创建对象之前，我们先去对应的类中寻找初始化方法`__init__`，了解我们需要传入的参数。

> 再次强调，python中初始化__init__是初始化方法，而__new__才是构造方法，一般情况下我们用户到构造方法，如果向要了解构造方法的只是，可以去我的python进阶中寻找答案

#### 放源码
- 源码我只放一部分，用于帮助理解创建链接

```python
class MongoClient(common.BaseObject):
    HOST = "localhost"
    PORT = 27017
    _constructor_args = ('document_class', 'tz_aware', 'connect')

    def __init__(
            self,
            host=None,
            port=None,
            document_class=dict,
            tz_aware=None,
            connect=None,
            **kwargs):
       """Client for a MongoDB instance, a replica set, or a set of mongoses.

        The client object is thread-safe and has connection-pooling built in.
        If an operation fails because of a network error,
        :class:`~pymongo.errors.ConnectionFailure` is raised and the client
        reconnects in the background. Application code should handle this
        exception (recognizing that the operation failed) and then continue to
        execute.

        The `host` parameter can be a full `mongodb URI
        <http://dochub.mongodb.org/core/connections>`_, in addition to
        a simple hostname. It can also be a list of hostnames or
        URIs. Any port specified in the host string(s) will override
        the `port` parameter. If multiple mongodb URIs containing
        database or auth information are passed, the last database,
        username, and password present will be used.  For username and
        passwords reserved characters like ':', '/', '+' and '@' must be
        percent encoded following RFC 2396::

            try:
                # Python 3.x
                from urllib.parse import quote_plus
            except ImportError:
                # Python 2.x
                from urllib import quote_plus

            uri = "mongodb://%s:%s@%s" % (
                quote_plus(user), quote_plus(password), host)
            client = MongoClient(uri)

        Unix domain sockets are also supported. The socket path must be percent
        encoded in the URI::

            uri = "mongodb://%s:%s@%s" % (
                quote_plus(user), quote_plus(password), quote_plus(socket_path))
            client = MongoClient(uri)

        But not when passed as a simple hostname::

            client = MongoClient('/tmp/mongodb-27017.sock')

        .. note:: Starting with version 3.0 the :class:`MongoClient`
          constructor no longer blocks while connecting to the server or
          servers, and it no longer raises
          :class:`~pymongo.errors.ConnectionFailure` if they are
          unavailable, nor :class:`~pymongo.errors.ConfigurationError`
          if the user's credentials are wrong. Instead, the constructor
          returns immediately and launches the connection process on
          background threads. You can check if the server is available
          like this::

            from pymongo.errors import ConnectionFailure
            client = MongoClient()
            try:
                # The ismaster command is cheap and does not require auth.
                client.admin.command('ismaster')
            except ConnectionFailure:
                print("Server not available")

        .. warning:: When using PyMongo in a multiprocessing context, please
          read :ref:`multiprocessing` first.
     
```

- 稍微总结下上面的内容，有以下几点
	1. 使用`pymongo.MongoClient()`实例化得到的client对象是线程安全的
	2. 在3.0之后的版本中，如果因为网络问题而导致链接失败就不会再抛出`~pymongo.errors.ConnectionFailure`，并且链接服务端的请求不会在阻塞线程


###### 参数
- host = None
	- mongodb服务器所在主机名
	- host有多种表示形式
		1. 主机IP, `172.96.203.6`	
		2. 主机IP+端口号，  `"mongo://172.96.203.6:27017"`
	-  hostname or IP address or Unix domain socket path of a single mongod or mongos instance to connect to, or a mongodb URI, or a list of hostnames / mongodb URIs. If `host` is an IPv6 literal it must be enclosed in '[' and ']' characters following the RFC2732 URL syntax (e.g. '[::1]' for localhost). Multihomed and round robin DNS addresses are **not** supported.
- port = None
	- mongodb服务器在该主机上的端口
	- port number on which to connect
- document_class = dict
	- default class to use for documents returned from queries on this client
- tz_aware = None
	- 如果为`True`: 从文档中查询得到的`datatime.datetime`对象就会被自动转换时区
	- :class:`~datetime.datetime` instances returned as values in a document by this :class:`MongoClient` will be timezone aware (otherwise they will be naive)
- connect = None
	- if ``True`` (the default), immediately  begin connecting to MongoDB in the background. Otherwise connect on the first operation.
- appname
	- (string or None) The name of the application that  created this MongoClient instance. MongoDB 3.4 and newer will  print this value in the server log upon establishing each connection. It is also recorded in the slow query log and  profile collections.
- replicaSet: The name of the replica set to connect to. The driver will verify that all servers it connects to match this name. Implies that the hosts specified are a seed list
 and the driver should attempt to find all members of the set. Defaults to `None`.
- readPreference: The replica set read preference for this client. One of  `primary `,  `primaryPreferred `,  `secondary` ,  `secondaryPreferred` , or ` nearest `. Defaults to  `primary` .
- username: 用户名
- password: 密码
	- **用户名和密码也可以写在host中**，格式如下:
		- `mongo://quote_plus(user):quote_plus(password)@172.96.203.6:27017`
		- quote_plus正确导入方式如下

```
try:
	# python3.x
	from urllib.parse import quote_plus
except ImportError:
	# python2.x
	from urllib import quote_plus
```

##### 创建链接

```
from pymongo import MongoClient


# client = MongoClient(host='172.96.203.6', port=27017)
client = MongoClient(host='mongodb://172.96.203.6:27017')

```


## 数据库操作
#### 选择一个数据库
- 在mongodb中，当数据库不存在的时候，`use db`会隐式的创建一个数据库，我们使用pymongo选择服务端中的一个数据库的时候，如果数据库不存在，也会被创建
- 创建一个数据库的语法如下

```
ziawang = client.ziawang		# 创建一个ziawang库

```


## 集合操作



## 文档操作
