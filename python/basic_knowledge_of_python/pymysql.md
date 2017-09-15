# pyMySQL模块
[pyMySQL官方指南](http://pymysql.readthedocs.io/en/latest/index.html#)
http://www.cnblogs.com/MySimond/p/4750199.html

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
- conn.begin()
	- 开始事务

- conn.rollback()
	- 回滚当前的事务

- conn.close()
	- 关闭链接

- conn.commit()
	- 将对数据库的更改提交到数据库服务器
	- Commit changes to stable storage

- conn.cursor(cursor=None)
	- 创建一个游标用来执行响应的数据库操作


- conn.ping(reconnect=True)
	- 检查服务端是否存在（正在运行）

- conn.select_db(db)
	- 设置当前操作的数据库

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
                       user='root',
                       password='pass',
                       database='userinfo',
                       charset='UTF8') #书写标准和MySQL相同，不能有横杠


cursor1 = conn.cursor()
cursor2 = pymysql.cursors.Cursor(conn)

print(cursor1)		
print(cursor2)
-----------------------------------------------------------
<pymysql.cursors.Cursor object at 0x0000021109609320>
<pymysql.cursors.Cursor object at 0x0000021109609390>
```

## Cursor Object Methods

- c.callproc(procname, args=())
- c.close()
	- 关闭一个游标，清除存在的数据（与该游标有关的）

- c.execute(query, args=None)
	- 执行一个sql语句
	- *parameters*
		- querys: 要执行的sql语句
		- args: 可以是一个元组，列表，字典，存放sql语句中要匹配的值
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
			-  [(),(),(),()]
			-  ((),(),(),())

> 查询操作

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
	-  返回所有满足条件的记录，类似生成器，但是对于非常大的结果集，这样做会非常占内存

- read_next()
	- 读去下一行记录

## pymysql.cursors.DictCursor(connection)
- a cursor which returns results as a dictionary

## pymysql.cursors.SSDictCursor(connection)
- a  unbuffered  cursor which return results as a dictionary 


