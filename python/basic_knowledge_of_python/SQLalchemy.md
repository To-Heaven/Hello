# SQLAlchemy ORM框架使用


#### 什么是ORM
- ORM即对象关系映射，ORM框架会把数据库中的表映射成python面向对象中的一个类，表中的一条记录映射成类实例化得到的一个对象，表中的每一个字段映射成类中的静态字段，以后在相对数据库操作的时候就不需要使用繁琐的SQL语句了，只需要通过操作类和对象就可以实现对数据库中表的操作。

#### ORM与原生SQL的比较（ORM的优缺点）
- 优点
	1. 提高开发效率。原先复杂易错的SQL语句被映射成了面向对象中的类和对象，方便了操作。

- 缺点
	1. 性能低于原生SQL。在ORM中通过类和对象操作数据库的时候，本质上最后还是使用原生SQL操作的数据库，ORM会将我们对类和对象的操作转换成响应的`SQL`语句
	2. ORM无法实现一些复杂的数据库操作。不过几乎所有的ORM框架都为我们提供了`原生SQL`的调用接口

#### Django内置ORM与SQLAlchemy的比较
- 就我个人的使用情况而言，Django内置的ORM在操作起来更加的方便和简单，比如它提供的`双下划线查询`，`创建多对多关系时自动生成第三张表`等很人性化。SQLAlchemy用起来更接近原生的SQL，很多的操作都需要我们自己来操作，比如多对多关系中建立第三张表时，我们就需要为第三张关系表创建一个类，在该类中指定关联字段，虽然比较繁琐，但是同时为我们提供了极大的灵活性。总之两者各有特色。

###### SQLAlchemy中创建和删除表
- `SQLAlchemy`中一旦通过类创建完一张表之后，默认的就不能再修改表结构（字段及表属性，不过我们可以使用`SQLAlchemy`提供的第三方组件来实现修改表结构
	- `Django内置的ORM`在修改表结构之后使用`python manage.py makemigrations ; python manage.py migrate`可以更新数据库中的表结构，

#### DBAPI
- 不管是`SQLAlchemy`还是`Django自带的ORM`，都需要通过python操作数据的模块`mysqldb/pymysql`来链接数据库。即**ORM框架依赖DBAPI，并且建立在DBAPI之上**


#### SQLAlchemy框架的结构
 `SQLAlchemy`是由一个个的功能组件构成，下面进行简单介绍

- `ORM`部分
	- object relationship mapping，即我们创建的模型类
	
- `Core`部分
	- `Engine`框架的引擎
	- `connection pool`框架中创建的数据库连接池
	- `dialect`用于匹配我们连接数据库的DBAPI，Dialect用于和数据API进行交流，根据配置文件的不同调用不同的数据库API，从而实现对数据库的操作
	- `SQL Exprression Language`用于将对类与对象的操作转换成对应的SQL语句（转换基于链接的数据库，因为即使都是关系型数据库，SQL语句仍然还是一些区别）
	- `Schema/Types`架构和类型

## 数据库操作
#### create_engine
###### 参数
- `name_or_url`用于指定要连接的`数据库服务器`以及`数据库相关配置`

```python
mysqldb
    mysql+mysqldb://<user>:<password>@<host>[:<port>]/<dbname>
    
pymysql
    mysql+pymysql://<username>:<password>@<host>/<dbname>[?<options>]
    
MySQL-Connector
    mysql+mysqlconnector://<user>:<password>@<host>[:<port>]/<dbname>
    
cx_Oracle
    oracle+cx_oracle://user:pass@host:port/dbname[?key=value&key=value...]
    
更多：http://docs.sqlalchemy.org/en/latest/dialects/index.html
```

- `max_overflow`超过连接池大小外最多创建的连接
	- `max_overflow+pool_size=最大真实连接数`
	
- `pool_size`连接池的大小，即连接池中连接数量的最大值
- `pool_timeout`线程获取链接的最长等待时间，超时将会报错
- `pool_recycle`用于重置连接池中的链接
	- 即重新创建一批链接替换旧链接

## 表操作
#### 配置表
###### `__tablename__`
- 这个静态字段用来配置表名称，该字段对应的值就是数据库中生成的表的名称

###### `__table_args__`
- 这个参数用来配置表，类似`Django Model类中的Meta`
- 常用的配置
	- 配置表属性（单个对象形式）
		- `UniqueConstraint`联合唯一
		- `Index`（联合）索引
	- 配置数据库属性（字典形式）
		- `"mysql_engine": "innodb"`配置MySQL的数据库引擎
		- `"mysql_charset": "utf8"`配置MySQL数据库的字符编码

- 使用
	- 有三种方式。
		- 当需要配置字典类型的配置时，直接将该字典赋值给`__table_args__`
		- 当需要配置对象类型的配置时，直接将对象放在元组中赋值给`__table_args__`
		- 当需要配置多种形式（字典、对象）的相关配置时，将该字典放在元组最后一个位置

```python
# 方式一

class MyClass(Base):
    __tablename__ = 'sometable'
    __table_args__ = (
            ForeignKeyConstraint(['id'], ['remote_table.id']),
            UniqueConstraint('foo'),
            )

# 方式二

class MyClass(Base):
    __tablename__ = 'sometable'
    __table_args__ = {'mysql_engine':'InnoDB'}


# 方式三
class MyClass(Base):
    __tablename__ = 'sometable'
    __table_args__ = (
            ForeignKeyConstraint(['id'], ['remote_table.id']),
            UniqueConstraint('foo'),
            {'autoload':True}
            )
```


#### 创建单个表
- 流程
	1. 导入模块
		- 用于创建`Base`类
			- `from sqlalchemy.ext.declarative import declarative_base`。本质上是导入了一个函数，该函数的返回值是一个用`DeclarativeMeta元类`创建的`Base`类
	2. 导入字段
		- `from sqlalchemy import Column`用于创建`Column`对象
		- `from sqlalchemy import Integer, String, Text, ForeignKey, DateTime, UniqueConstraint, Index`用于创建不同类型的字段
	3. 获取`Base`类
	4. 创建`Base`的派生类

- 其他
	- 派生类的静态字段`__tablename__`用来指定该类在**数据库中对应的表的名称，该名称在后面的联表操作中将会用到**
	- 派生类的`__table_args__`类似`Django内置ORM的Meta类`，用于配置表属性。比如配置`联合索引`、`联合唯一`等




```python
import datetime

from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column
from sqlalchemy import Integer, String, UniqueConstraint, DateTime


Base = declarative_base()


class Users(Base):
    """ 用户登录信息表 """

    __tablename__ = "users"

    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(length=32), nullable=False, index=True)
    password = Column(String(length=32), nullable=False)
    join_time = Column(DateTime, default=datetime.datetime.now)

    __table_args__ = (
        UniqueConstraint('id', 'username', name='unique_id_username'),	 
        {
            "mysql_engine": "innodb",
            "mysql_charset": "utf8",
        }
    )

``` 

###### 注意
1. `String`字段类型可以使用`length`参数限制字符串的最大长度
2. 创建`时间类型字段`时，`default`对应的是一个可执行函数，不要传入一个返回值，这样会导致创建的所有用户时间都相同并且时固定不变的，即**不要使用`default=datetime.datetime.now()`**。

#### 创建多个表（一对多 or 多对一）
- 使用`ForeignKey`创建外键关系
	- 以`图书表`和`出版社表`为例，一个`出版社`可以对应多个`图书`，因此`ForeignKey`字段要放在`图书表中`
	- 流程
		1. 在`图书表`中创建关联字段`publish_id`
		2. 在`publish_id`中使用`ForeignKey`创建关联关系。这就像我们使用原生SQL创建外键关系思路完全一样

- 使用`relationship`创建反向关联	
	- `relationship`与生成表结构完全无关；它是SQLAlchemy提供给我们方便查询的一个工具
	- 参数
		- 第一个参数用来指定要关联的`主键表的类名`，注意是类名
		- 第二个参数用来指定，反向关联时要使用的名称，比如当想要通过`出版社`获取所有关联`图书`对象时，就需要用到该反向关联

- 对`ForeignKey`使用`onupdate`和`ondelete`参数
	- 保证在删除或修改关联表数据时实现数据的同步修改

```python
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column
from sqlalchemy import Integer, String, DECIMAL, Index
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship


Base = declarative_base()



class Book(Base):
    """ 图书表 """

    __tablename__ = "books"

    id = Column(Integer, primary_key=True, autoincrement=True)
    book_name = Column(String(length=32), nullable=False)
    price = Column(DECIMAL, nullable=False)

    publish_id = Column(Integer, ForeignKey(column="publishs.id"))
    publish = relationship("Publish", backref="books")

    __table_args__ = (
        Index("index_book_publish", "book_name", "publish_id"),
        {
            "mysql_engine": "innodb",
            "mysql_charset": "utf8",
        }
    )


class Publish(Base):
    """ 出版社表 """

    __tablename__ = "publishs"

    id = Column(Integer, primary_key=True, autoincrement=True)
    publish_name = Column(String(length=32), nullable=False, index=True)

```

###### 注意
1. 创建`ForeignKey`的时候，`column`参数对应的是`数据库的表中的字段`，而不是`类的静态字段`
2. `relationship`中，第一个参数指定的是`类名称`，因为与生成表结构完全无关
3. `Index`创建`联合索引时`，需要按照位置传参的方式，将创建索引名称放在第一位。（与`UniqueConstraint`不同）

#### 创建多个表（多对多）

```python
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column
from sqlalchemy import Integer, String, DECIMAL, Index
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship


Base = declarative_base()


class Authors(Base):
    __tablename__ = "authors"

    id = Column(Integer, primary_key=True, autoincrement=True)
    author_name = Column(String(length=32), nullable=False, index=True)

    books = relationship("Books", secondary="authors2books", backref="books")


class Books(Base):
    """ 图书表 """

    __tablename__ = "books"

    id = Column(Integer, primary_key=True, autoincrement=True)
    book_name = Column(String(length=32), nullable=False)
    price = Column(DECIMAL, nullable=False)

    publish_id = Column(Integer, ForeignKey(column="publishs.id", onupdate='CASCADE', ondelete='CASCADE'))
    publish = relationship("Publishs", backref="books")

    __table_args__ = (
        Index("book_name", "publish_id", name="index_book_publish"),
        {
            "mysql_engine": "innodb",
            "mysql_charset": "utf8",
        }
    )

class Authors2Books(Base):
    __tablename__ = "authors2books"

    id = Column(Integer, primary_key=True, autoincrement=True)
    author_id = Column(Integer, ForeignKey("authors.id", onupdate='CASCADE', ondelete='CASCADE'))
    book_id = Column(Integer, ForeignKey("books.id", onupdate='CASCADE', ondelete='CASCADE'))

    __table_args__ = (
        UniqueConstraint("author_id", "book_id", name="unique_author_book"),
        {
            "mysql_engine": "innodb",
            "mysql_charset": "utf8",
        }
    )

class Publishs(Base):
    """ 出版社表 """

    __tablename__ = "publishs"

    id = Column(Integer, primary_key=True, autoincrement=True)
    publish_name = Column(String(length=32), nullable=False, index=True)
```

###### 注意
1. 创建多对多关系使用`relationship`时，我们需要在`relationsship`的`secondary`参数中指定`用于存放多对多关系的第三方关系表的名称`，第一个参数仍然是`类名`

#### 生成表 and 删除表
###### 步骤
1. 创建`engine`
2. 使用`Base`类将类操作`engine`

###### 代码

```python
engine = create_engine(
            name_or_url="mysql+pymysql://flask:passflask@172.96.203.6:3306/f0111?charset=utf8",
            max_overflow=0,
            pool_size=5,
            pool_timeout=30,
            pool_recycle=-1,
        )


class TableOperation(object):
    def __init__(self, base_class):
        self.engine = engine
        self.base_class = base_class

    def drop_all(self):
        """ 删除数据库所有表 """
        self.base_class.metadata.drop_all(self.engine)

    def create_all(self):
        """ 创建所有表 """
        self.base_class.metadata.create_all(self.engine)


opeationer = TableOperation(Base)
opeationer.create_all()
```
 
 
#### 约束
- 参考[官方文档](http://docs.sqlalchemy.org/en/latest/core/constraints.html?highlight=foreignkey#sqlalchemy.schema.ForeignKeyConstraint.argument_for "官方文档")

###### 唯一约束
1. 在`Column`创建字段时，`index=True`参数指定该字段用来创建索引
2. 在`__table_args__`中使用`Index(index_name, *fields)`指定

###### 检查约束
- 


###### 主键约束

###### 是否为null


## 使用session操作数据库表
- 在每一次操作数据库操作之前，都需要创建一个`session`，通过`session`才能操作数据库

#### SQLAlchemy 中创建session的两种方式
**方式1**
- 步骤
	1. 导入`from sqlalchemy.orm import sessionmaker`,
	2. 创建`engine`
	3. 实例化`sessionmaker`并将`engine`传递给`bind`参数
	
```python
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine
from models import Users
  
engine = create_engine("mysql+pymysql://手动马赛克@172.96.203.7:3306/flaskhaha", max_overflow=0, pool_size=5)
Session = sessionmaker(bind=engine)

# 中间执行操作

session.close()			# 关闭session
```

- 这种方式的缺点
	1. 线程不安全

- 注意
	1. 这种方式创建得到的是一个`Session`对象
	2. 每次执行数据库操作时，都需要创建一个session
	3. 每次操作完数据，都要记得**关闭session**
	

**方式2**
- 步骤
	1. 导入
		1. `from sqlalchemy.orm import sessionmaker`
		2. `from sqlalchemy.orm import scoped_session`
	2. 创建`engine`
	3. 使用`engine`实例化`sessionmaker`得到`session`对象
	4. 将`session`对象作为参数传递给`scoped_session`

```python
from sqlalchemy.orm import sessionmaker
from sqlalchemy.orm import scoped_session
from sqlalchemy import create_engine
  
engine = create_engine("mysql+pymysql://手动马赛克@172.96.203.7:3306/flaskhaha", max_overflow=0, pool_size=5)

Session = sessionmaker(bind=engine)
session = scoped_session(Session)

# 中间执行操作

session.close()			# 关闭session
```

- 注意
	1. 这种方式得到的session是线程安全的，它是**基于本地线程Local实现的，当线程操作数据库的时候，都会给每一个线程创建一个私有的session对象**
	2. scoped_session中有原来方法的Session中的所有方法，也就是说，我们可以想操作之前`sessionmaker`得到的`session`对象一样操作通过`scoped_session`得到的`session`

###### `scoped_session`源码分析

## 数据操作
- 数据操作是通过`session`对象来完成的

#### 插入数据
###### 1. 插入普通数据
**插入单条**

```python
session = scoped_session(
    sessionmaker(bind=engine)
)

user_obj = Users(username="ziawang", password="pass")
session.add(user_obj)
session.commit()
session.close()
```

**插入多条**

```python
session = scoped_session(
    sessionmaker(bind=engine)
)

user_obj = Users(username="ziawang", password="pass") 

session.add_all([
    Users(username='stark', password='pass'),
    Users(username='lusi', password='pass'),
])
session.commit()
session.close()
```



###### 2. 插入数据，包含多对一/一对多关系

**方式1**

```python
session = scoped_session(
    sessionmaker(bind=engine)
)

publish_obj = Publishs(publish_name='人民出版社')
publish_obj.books = [
    Books(book_name='python从入门到放弃', price=100.00),
    Books(book_name='mysql从入门到放弃', price=200.00),
]

session.add(publish_obj)
session.commit()
session.close()
```

**方式2**

```python
session = scoped_session(
    sessionmaker(bind=engine)
) 
 

session.add_all([
    Publishs(publish_name='教育出版社'),
    Books(book_name='linux从入门到放弃', price=130.00, publish_id=1),
    Books(book_name='redisl从入门到放弃', price=220.00, publish_id=2),

])


session.commit()
session.close()
```

**方式3**

```python
session = scoped_session(
    sessionmaker(bind=engine)
) 
 

session.add_all([
    Books(book_name='javascript从入门到放弃', price=150.00, publish=Publishs(publish_name="科技出版社")),
    Books(book_name='java从入门到放弃', price=350.00, publish=Publishs(publish_name="农业出版社"))
])

session.commit()
session.close()
```

###### 3. 插入数据，包含多对多关系

**方式1**
- 就像我们使用`原生sql`插入多对多关系数据一样，我们可以先分别在各自表中创建记录对象，然后在关系表中插入关系

```python
session = scoped_session(
    sessionmaker(bind=engine)
)

session.add_all([
    Books(book_name='html从入门到精通', price=130.00, publish_id=1),
    Books(book_name='css从入门到精通', price=130.00, publish_id=3),
    Authors(author_name='ziawang'),
    Authors(author_name='liux'),
])


session.add_all([
    Authors2Books(author_id=1, book_id=2),
    Authors2Books(author_id=2, book_id=2),
    Authors2Books(author_id=1, book_id=1),
    Authors2Books(author_id=2, book_id=1),
])

session.commit()
session.close()
```

**方式2**

```python
session = scoped_session(
    sessionmaker(bind=engine)
)

author = Authors(author_name='ziawang')
author.books = [
    Books(book_name='html从入门到精通', price=130.00, publish_id=1),
    Books(book_name='css从入门到精通', price=130.00, publish_id=3),
]

session.commit()
session.close()
```



**方式3**

```python
session = scoped_session(
    sessionmaker(bind=engine)
)

book = Books(book_name='html从入门到精通', price=130.00, publish_id=1)
book.authors = [
    Authors(author_name='ziawang'),
    Authors(author_name='liux'),
]

session.commit()
session.close()
```
 
#### 修改数据
###### 直接修改值

```python
session.query(Users).filter(Users.id > 0).update({"name" : "wangzihao"})
```

###### 在原有的基础上通过表达式修改字段值
**操作字串**
- 操作字符串时，需要指定参数`synchronize_session=False`

```python
session.query(Users).filter(Users.id > 0).update({Users.name: Users.name + " _CoolMan"}, synchronize_session=False)
```

**操作数字**
- 操作数数值的时候，需要指定参数`synchronize_session="evaluate"`


```python
session.query(Users).filter(Users.id > 0).update({"age": Users.age + 1}, synchronize_session="evaluate")
```


#### 删除数据
- 直接调用结果集的`delete()`方法即可

```python
session.query(Users).filter(Users.id > 2).delete()
 
```

#### 使用原生SQL
> show status like "%thread%";

###### 方式一
- 使用`session`对象的`execute`方法

```python
session = scoped_session(
    sessionmaker(bind=engine)
)

# 查询
cursor = session.execute('select * from users')
result = cursor.fetchall()

# 添加
cursor = session.execute('insert into users(name) values(:value)',params={"value":'wupeiqi'})
session.commit()

session.close()
```

###### 方式二
- 使用`row_connection`

```python
conn = engine.raw_connection()
    cursor = conn.cursor()
    cursor.execute(
        "select * from users"
    )
    result = cursor.fetchall()
    cursor.close()
    conn.close()
```

#### 查询数据
**1. 查询表中所有记录的所有字段值**

- `session.query()`中直接以模型类作为参数时，表示查询所有的字段

```python
session.query(Users).all()
```

**2. 查询表中所有记录的指定字段值**
- 在`session.query()`方法中，可以通过模型类指定要查询的字段
- 对字段使用`label(alias)`方法可以指定字段别名，相当于`SQL`语句中的`as`关键字

```python
session.query(Users.name.label('nm'), Users.age).all()
```

**3. 使用表达式筛选数据**



- `filter(expression)`	
	- expression有两种方式
		1. 直接是使用条件表达式，返回`True/False`
		2. 使用`text函数`来动态生成表达式
			- text参数是一个字符串，字符串中使用`:var`作为占位符，调用`text`返回值的`params(**kwargs)`方法来指定要填充的数据


```python
session.query(Users).filter(Users.name == "alex").all()

session.query(Users).filter(text("id<:value and name=:name")).params(value=20, name='ziawang').order_by(Users.id).all()

```


**4. 使用参数筛选数据**

- `filter_by(**kwargs)`

```python
session.query(Users).filter_by(name='ziawang').all()

session.query(Users).filter_by(name='ziawang').first()
```

**5. 使用原生SQL**

- `from_statement()`配合`text`可以实现

```python
session.query(Users).from_statement(text("SELECT * FROM users where name=:name")).params(name='wangzihao').all()
```


**6. filter中表达式详细操作**

- 多个键值对参数之间是`and`关系

```python
session.query(Users).filter(Users.id > 1, Users.name == 'ziawang').all()
```

- 使用`between`处理一定范围内的数值
	- `between(1, 3)`包括`1, 2, 3`

```python
session.query(Users).filter(Users.between(1, 3), Users.name == 'ziawang').all()
```

- 使用`in`处理`存在于`判断

```python
session.query(Users).filter(Users.id.in_([1,3,4])).all()
```

- `~ 非`

```python
session.query(Users).filter(~Users.id.in_([1,3,4])).all()
```

- 使用`子查询`

```python
session.query(Users).filter(Users.id.in_(session.query(Users.id).filter_by(name='ziawang'))).all()
```

- 使用`and_`组装`与`关系条件
	- 需要导入`form sqlalchemy import and_` 

```python
session.query(Users).filter(and_(Users.id > 3, Users.name == 'ziawang')).all()
```

- 使用`or_`组装`或`关系条件
	- 需要导入`form sqlalchemy import and_` 

```python

session.query(Users).filter(or_(Users.id < 2, Users.name == 'ziawang')).all()
```

- 使用`and_, or_`组装条件

```python
session.query(Users).filter(
    or_(
        Users.id < 2,
        and_(Users.name == 'ziawang', Users.id > 3),
        Users.extra != ""
    )).all()
```

- 模糊搜索
	- 使用字段对象的`like(str_expression)`方法

```python
session.query(Users).filter(~Users.name.like('xxx%')).all()
```

- 对结果集切片操作

```python
session.query(Users)[1:2]
```


- 排序
	- `desc()`
		- 降序排列
	- `asc()`
		- 升序排列

```python
session.query(Users).order_by(Users.name.desc(), Users.id.asc()).all()
```


- **分组操作**
	- 如果要使用`聚合函数`，需要导入`from sqlalchemy.sql import func`
	- `func`对象内部封装了各种`聚合功能`的方法
		- `max`
		- `min`
		- `sum`
	- 使用`having`可以对分组结果进行再次筛选
	
	

```python
session.query(Users).group_by(Users.extra).all()

session.query(
    func.max(Users.id),
    func.sum(Users.id),
    func.min(Users.id)).group_by(Users.name).all()


ret = session.query(
    func.max(Users.id),
    func.sum(Users.id),
    func.min(Users.id)).group_by(Users.name).having(func.min(Users.id) >2).all()
```

- 联表查询
	- 默认使用`inner join`

```python
session.query(Books, Authors).filter(Books.id == Authors.id).all()
```

- `union`组合

```python
q1 = session.query(Books.name).filter(Books.id > 2)
q2 = session.query(Authors.name).filter(Authors.id < 2)
ret = q1.union(q2).all()


q1 = session.query(Books.name).filter(Books.id > 2)
q2 = session.query(Authors.caption).filter(Authors.nid < 2)
ret = q1.union_all(q2).all()
```

- 使用`relationship`查询外键字段

```python
```


- 使用`relationship`查询多对多字段

```python

```

- 链表操作时，对`on`条件使用`and_, or`

```python

```


- **关联子查询**

```python
subqry = session.query(func.count(Server.id).label("sid")).filter(Server.id == Group.id).correlate(Group).as_scalar()

result = session.query(Group.name, subqry)

"""
SELECT `group`.name AS group_name, (SELECT count(server.id) AS sid 
FROM server 
WHERE server.id = `group`.id) AS anon_1 
FROM `group`
"""
```


## 使用sqlalchemy-utils组件
- Django中，我们可以对字段使用`choices`，原生的`SQLAlchemy`并没有提供这个功能，我们需要配合其`sqlalchemy-utils`组件实现



```python
import datetime
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column
from sqlalchemy import Integer, String, UniqueConstraint, DateTime
from sqlalchemy_utils import ChoiceType

Base = declarative_base()


class Users(Base):
    """ 用户登录信息表 """

    __tablename__ = "users"

    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(length=32), nullable=False, index=True)
    password = Column(String(length=32), nullable=False)
    join_time = Column(DateTime, default=datetime.datetime.now)

    gender_types = [
        (1, 'male'),
        (2, 'female'),
    ]
    gender = Column(ChoiceType(gender_types, impl=Integer()))
    __table_args__ = (
        UniqueConstraint('id', 'username', name='unique_id_username'),
        {
            "mysql_engine": "innodb",
            "mysql_charset": "utf8",
        }
    )

# ----------分割线
# 数据库中对应的表创建信息如下

"""

mysql> show create table users\G;
*************************** 1. row ***************************
       Table: users
Create Table: CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(32) NOT NULL,
  `password` varchar(32) NOT NULL,
  `join_time` datetime DEFAULT NULL,
  `gender` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `unique_id_username` (`id`,`username`),
  KEY `ix_users_username` (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)

ERROR: 
No query specified

"""

```












