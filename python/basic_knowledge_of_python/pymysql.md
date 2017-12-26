# pyMySQL模块
[pyMySQL官方指南](http://pymysql.readthedocs.io/en/latest/index.html#)
http://www.cnblogs.com/MySimond/p/4750199.html

## pymysql应用的场景
#### 先说说Django的ORM
- Django有自己内置的ORM框架，因此**不需要我们自己考虑去链接数据库**
- Django本身链接数据库，本质上也是得使用mysldb/pymysql
	- 如果你用的时`python3`，你需要在应用的`__init__.py`文件中加上这一段代码

```python
import pymysql


pymsql.install_as_MySQLdb()
```

- DJango的ORM有优点也有缺点
	- 优点：
		- 当我们要操作数据库时，不需要再使用元僧的SQL语句与数据库交互，只需要使用ORM为我们提供的类与对象来操作数据库。这样使得我们创建和修改数据库表的时候非常方便，如果没有ORM，我们直接操作数据库的话，就得使用mysql命令或者配合navicate mysql来操作数据
		- 使用ORM的时候我们不需要考虑数据库的链接，只需要专注于数据库的操作，ORM会帮助我们将对象和类的操作转换成会响应的SQL语句
	- 缺点：
		- 速度上会比原生的SQL要慢，因为ORM将对象和类转换成SQL语句的时候需要消耗时间。
		- 通过原生的SQL可以构造出复杂的语句，而使用ORM可能很难实现这些复杂查询
		

#### 应用场景
###### 1. flask
- flask中你可以使用`SQLalchemy`，也可以直接使用`pymysql`来链接数据库

###### 2. tornado


###### 3.非Web的脚本程序
- 最常见的比如爬虫程序

###### 4. 自己动手实现一个增删改查的组件类
- 使用pymysql我们可以自己将pymysql对数据库的增删改查功能封装到一个类中，这样就没有必要每一次都写重复的代码了
- 但是有几点一定要注意！！
	1. 对Connection对象使用单例模式。为什么要用单例模式？很简单，如果你不适用单例模式，每一次对数据库的操作可能都会重复`建立连接`这个步骤，这样显然是有问题的，我们要避免不必要的重复链接，因为每一次的数据库连接都会产生性能损耗。我们可以利用python的模块导入的特性来实现`单例模式`
	2. 链接数据库的`connect`参数应该存放在配置文件中进行获取

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

- 测试`connection`对象的是否是实现了单例

```python
from curd import MysqlCURD


a = MysqlCURD()
b = MysqlCURD()

print(id(a))        # 49984464
print(id(b))        # 49984592
print(id(a.connection))  # 53963184
print(id(b.connection))  # 53963184	，证明实现了单例模式	
```



## 获取Connection对象(pymysql.connections.Connection)
- 本质上是一个通过socket套接字建立的和MySQL服务端之间的链接
- 有两种方式获取
	- 最常用的是： 
		- `conn = pymysql.connect(parameters)`
	- 还可以通过创建类实例的方法
		- `conn = pymysql.connections.Connection(parameters)`
	
	- *patameters*（部分）
		- host:  目标MySQL服务器的IP地址
		- port:  目标MySQL服务端的端口
		- user:   要登录的用户
		- password:  该用户对应的密码
		- database:    指定要使用的数据库（应该在用户权限下的数据库）
		- charset:   指定想要使用的编码，编码书写格式要与MySQL一致，比如python中的'utf-8'要换成'utf8' 或'UTF8'
		- bind_adress: 当本地客户端有多个链接到MySQL服务端的网络接口时，用于指定链接到服务端的网络接口。可以是一个IP地址或hostname
		- unix_socket: 套接字家族设置为  AF_UNIX 而不是 AF_INET
		- read_default_file:   指定从配置文件`my.ini`中去获取配置来代替上述参数
		- max_allowed_packet:  发送给服务端数据的最大字节数，只能用导入数于据的SQL语句`LOAD LOCAL INFILE . . . `。 default16MB
		- local_infile:   布尔值，如果为真，则允许使用'LOAD DATA INFILE.. . . ' 命令，默认为False
		- autocommit:  走动将对数据库的操作提交到数据库服务端
		- cursorclass:  指定要使用的cursor 类，默认为`cursor`
			- SSCursor
			- DictCursor
			- SSDictCursor

## Connection  Object Methods
#### 事务相关
- conn.begin()
	- 开始事务

- conn.rollback()
	- 回滚当前的事务

#### 链接相关


- conn.ping(reconnect=True)
	- 检查服务端是否存在（正在运行）


- conn.close()
	- 关闭链接

- conn.commit()
	- 将对数据库的更改提交到数据库服务器
	- Commit changes to stable storage

#### 数据库相关
- conn.select_db(db)
	- 设置当前操作的数据库

#### 游标相关

- conn.cursor(cursor=None)
	- 创建一个游标用来执行响应的数据库操作


#### 其他

- conn.show_warnings()
	- 显示警告

## Connection Object Variables
- conn.autocommit_mode
	- **默认为None**
	- 如果为True，则设置为自动提交模式



## Cursor object 
- 获取Cursor对象
	- 使用connection_obj.cursor()
	- 类实例化
		- `c = pymysql.curcors.Cursor(conn)`
	- 注意：
		- 对于同一个`conn`对象，使用上述两种方法得到的是两个不同的对象


```python
import pymysql


conn = pymysql.connect(host='localhost',
                       port=3306,
                       user='root',
                       password='pass',
                       database='review',
                       charset='utf8',
                       cursorclass=pymysql.cursors.DictCursor,
                       autocommit=True)

print(conn.ping(reconnect=False))

c1 = conn.cursor(cursor=pymysql.cursors.DictCursor)
c2 = pymysql.cursors.SSCursor(conn)

print(c1)
print(c2)
# -------------------result-----------------------------
<pymysql.connections.OKPacketWrapper object at 0x02EC7830>
<pymysql.cursors.DictCursor object at 0x02BEB210>
<pymysql.cursors.SSCursor object at 0x02EC7830>
```

## Cursor Object Methods
#### 存储过程
- c.callproc(procname, args=())

#### 游标操作

- c.close()
	- 关闭一个游标，清除存在的数据（与该游标有关的）

#### 执行操作
- c.execute(query, args=None)
	- 执行一个sql语句
	- *parameters*
		- querys: 要执行的sql语句
		- args: 可以是一个元组，列表，字典，存放sql语句中"占位符"要匹配的值
			- 如果是列表或元组：sql语句中可使用%s占位符
			- 如果是字典：sql语句中可使用%(name)s 
	- 返回值为被影响了的记录条数  Number of affected rows
	- **返回值为int类型**

- c.executemany(query, args)
	- 针对一个sql语句执行多个数据
		- 实现insert插入时，插入多条数据
	- *parameters*
		-  querys: 要执行的sql语句
		-  args: 由序列或映射关系构成的序列
			-  可以是：[(),(),(),()]
			-  也可以是：((),(),(),())




#### 查询操作

- fetchone()
	- 查询满足条件一条记录
- fetchmany(size=None)
	- 查询满足条件多条记录
- fetchall()  
	- 查询满足条件所有记录

- mogrify(query, args=None)
	- 识别sql语句，防止sql注入
	- execute也实现了mogrify方法

```python
import pymysql


conn = pymysql.connect(host='localhost',
                       user='root',
                       password='pass',
                       database='userinfo',
                       charset='UTF8')
cursor = conn.cursor()


user = input('name>>>').strip()
password = input('password>>>').strip()

sql = 'select * from user where user="%s" and password="%s"'%(user, password)
sql1 = cursor.mogrify(sql)
print(sql1)
rows = cursor.execute(sql1)
print(rows)
--------------------------------------------------------
name>>>root" -- adasd
password>>>
select * from user where user="root" -- adasd" and password=""
0

Process finished with exit code 0
```

## Cursor Object Variabels
- c.max_stmt_length = 1024000
	- c.executemany()方法生成语句的最大大小


## SSCursor Object
- pymysql.cursors.SSCursor(connection)
	- 返回一个无缓冲的cursor对象。（主要用于服务端针对发送的sql命令返回比较大的数据，或者网络较慢的情况下使用）
	- 这个cursor对象并没有将所有的服务端数据加载到缓冲区，而是根据查询需要来返回对应行。
	- 优点：
		- 能够有效处理网速较慢或者结果集非常大的场景
	- 缺点：
		- 无法得出结果集的总长度（只能使用遍历的方式获取）
		- 只有当前查询的记录被加载到内存
	
## SSCursor Methods
- fetchone()
- fetchall()
- fetcmany()
- fetchall_unbuffered()
	-  返回所有满足条件的记录，类似迭代器，但是对于非常大的结果集，这样做会非常占内存

- read_next()
	- 读去下一行记录

## pymysql.cursors.DictCursor(connection)
- a cursor which returns results as a dictionary

## pymysql.cursors.SSDictCursor(connection)
- a  unbuffered  cursor which return results as a dictionary 


