# pymongo模块
- 类似pymysql，pymongo是python提供的可以用来与mongodb服务器进行交互的模块
- 底层与数据库的链接也是通过socket实现的

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

## mongo中的惰性
- 当我们在mongo中创建一个数据库或collection的时候本质上是惰性创建的，只有当数据库或者集合collection中插入第一条数据的时候，该数据库或者集合才会被创建

## 数据库操作
#### 选择一个数据库
- 在mongodb中，当数据库不存在的时候，`use db`会隐式的创建一个数据库，我们使用pymongo选择服务端中的一个数据库的时候，如果数据库不存在，也会被创建
- 创建一个数据库的语法如下
	- 可以使用访问属性的方式获取数据库或创建一个数据库
	- 如果通过属性访问数据库失败，我们还可以使用中括号类似字典的形式访问数据库

```
>>> ziawang = connect.ziawang
>>> ziawang
Database(MongoClient(host=['172.96.203.6:27017'], document_class=dict, tz_aware=False, connect=True), 'ziawang')
```

```
>>> ziawang = connect["ziawang"]
>>> ziawang
Database(MongoClient(host=['172.96.203.6:27017'], document_class=dict, tz_aware=False, connect=True), 'ziawang')
>>> 

```
#### 添加用户
- `db.add_user(name, password=None, read_only=None, **kwargs)`
	- 这个方法会创建一个拥有操作该数据库指定权限的新用户，**如果这个用户在之前已经存在，password参数会重置该用户的密码**
	- read_only是赋予该用户**只读**的权限

#### 删除用户
- `db.remove_user(name)`
	- 从指定数据库中删除指定用户
	- **被删除的用户将不会再具备访问该数据库的权限**


## 集合操作

#### 创建一个集合
- mongodb中的集合类似MySQL中的表，在pymongo中，获取一个集合collection的操作和操作数据库一样。我们可以使用访问属性的方式或者类似字典的形式

```
>>> test_collection = ziawang.test_collection
>>> test_collection
Collection(Database(MongoClient(host=['172.96.203.6:27017'], document_class=dict, tz_aware=False, connect=True), 'ziawang'), 'test_collection')
```

```
>>> test_collection = ziawang['test_collection']
>>> test_collection
Collection(Database(MongoClient(host=['172.96.203.6:27017'], document_class=dict, tz_aware=False, connect=True), 'ziawang'), 'test_collection')
>>> 
```

#### 查看指定数据库下的集合
- 使用`db.collection_names(include_system_collections=True)`
	- 这个方法会将指定db数据库下的所有集合名称以字符串存放在列表中返回
	- 当`include_system_collections=True`的时候，会列出指定数据库下的`system collection`，比如`system.indexs`

```python
>>> client = MongoClient(
	host="mongodb://172.96.203.6:27017"
	)
>>> db = client.ziawang
>>> db.collection_names()
['article']
>>> 
```

#### 删除一个集合
- 使用`db.drop_collection(name_or_collection)`
	- 这个方法会删除指定数据库下的一个集合
	- 参数可以是集合的名字也可以是pymongo中的一个集合对象


## 文档操作
- mongo中的document对应MySQL中的一行记录。在mongo中，一个document文档是以json对象表示的，在pymongo中我们可以使用字典来表示document

#### 自动转换
- 在pymongo中，我们可以使用python原生的数据类型比列表，datetime对象，他们将会被自动转换成BSON对象存储在MongoDB数据库中

#### 插入文档
###### 插入一条数据
- 插入一条数据使用collection对象的insert_one(json_obj)方法
	- 该方法返回一个**InsertOneResult**对象
- 比如现在创建一个数据库，文章数据库下包含一个文章表，文章表下存放文章的详细信息比如文章类型、文章关键字、文章创建时间、文章作者等

```python
from pymongo import MongoClient
import datetime


# connect = MongoClient(
#     host='172.96.203.6',
#     port=27017
# )

client = MongoClient(
    host='mongodb://172.96.203.6:27017'
)

db = client.ziawang     # 创建一个ziawang数据库
article = db.article    # 创建一个article集合

article_a = {
    "author": "ziawang",
    "createTime": datetime.datetime.utcnow(),
    "keys": ["Python", "MongoDB", "Dajngo"],
    "type": "code"
}

obj = article.insert_one(article_a)
print(obj)
```

- 现在进入mongo客户端查看结果 

```
> show dbs
admin    0.000GB
local    0.000GB
ziawang  0.000GB
> use ziawang
switched to db ziawang
> show tables;
article
> db.article.find()
{ "_id" : ObjectId("5a21660e40ef060230d82d80"), "author" : "ziawang", "createTime" : ISODate("2017-12-01T14:24:14.147Z"), "keys" : [ "Python", "MongoDB", "Dajngo" ], "type" : "code" }
> 

```


###### 批量插入多条文档
- 和mongodb中操作一样，批量插入使用的是collection对象的`insert_many()`方法。该方法的参数是由json对象组成的一个列表

```python
collection.insert_many(
	{"key1": "value1"},
	{"key2": "value2"}
)

```




#### 查询文档
###### 查询一条文档
- `collection.find_one(filter=None, *args, **kwargs)`
	- 从数据库中返回第一条满足匹配的文档对象
	- filter是一个**封装了查询条件的字典**，我们可以使用类似在mongo中的操作一样去查询数据
	- 当查询条件为None的时候，返回的是集合中第一个文档对象
- 源码如下

```
    def find_one(self, filter=None, *args, **kwargs):
        if (filter is not None and not
                isinstance(filter, collections.Mapping)):
            filter = {"_id": filter}

        cursor = self.find(filter, *args, **kwargs)
        for result in cursor.limit(-1):					# 只返回第一条文档对象
            return result
        return None
```

###### 查询多条文档
- `collection.find(*args, **kwargs)`
	- 返回一个`Cursor`对象，再mongodb中也是返回一个游标对象
	- filter:查询语句 
		- a SON object specifying elements which must be present for a document to be included in the result set
	- projection: 指定要返回的字段 
		- a list of field names that should be returned in the result set or a dict specifying the fields to include or exclude.
		- 比如 `projection={'_id': False}` 表示不显示字典，如果使用列表的方式来指定要显示的字段，那么`_id`字段将总是会在结果中出现
	- skip: 从开始位置跳过指定数量的查询结果再返回查询结果集
	- limit: 要返回结果的最大数量
	- sort：为查询结果指定用来写排序的字段

- 关于Cursor对象
	- 特点
		- Cusor对象是可迭代的存项，包含了查询的结果集
		- Cursor对象可以使用
	- 更做Cursor对象的操作方法见下面


###### 查询指定集合下文档的数量
- `db.collection.count()`
	- 返回collection下所有文档的个数

- `db.collection.find({"age": {"$gt": 18}}).count()`
	- 返回collection下年龄字段值大于18的的所有文档的个数







## Cursor对象
- 集合对象的find()方法会返回一个Cursor对象可以看作是一个结果集，我们可以使用Cursor对象的方法实现各种查询操作。

#### c.limit(limit)
- 限制返回的结果数量
- 参数limit是一个整数

#### c.skip(skip)
- 从开始位置跳过指定数量的查询结果再返回查询结果集
- skip是一个整数，表示要跳过的数量


#### c.max(spec)
- spec是一个由字段组成的列表或者元组

#### c.min(spec)


#### c.sort()
