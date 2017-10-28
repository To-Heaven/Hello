## NOSQL

## MongoDB与SQL比较


```
SQL 		---------> 		 MongoDB

database		数据库		database

table			表			collection

row 			一行记录		document

column			字段			field

index			索引			index

primary key		主键			_id自动设置为主键

```

- mysql中的两条记录

```sql
mysql> select * from info;
+----+-----------+------------------------+------+---------+
| id | name      | email                  | age  | city    |
+----+-----------+------------------------+------+---------+
|  1 | ziawang   | ziawang_python@163.com |   23 | beijing |
|  2 | wangdabao | xxx@qq.com             |   23 | beijing |
+----+-----------+------------------------+------+---------+
2 rows in set (0.00 sec)

mysql>
```

- 在mongodb中的存储形式

```
{
	"_id": ObjectId("xxxxxxxxxxxx"),
	"name": "ziawang",
	"email": "ziawang_python@163.com",
	"age": 23,
	"city": "beijing"
}
{
	"_id": ObjectId("xxxxxxxxxxxxx"),
	"name": "wangdabao",
	"email": "xxx@qq.com",
	"age": 23,
	"city": "beijing"
}

```

#### mongodb 与 redis、memcached区别
- redis和meemcached是key/value数据库
	- 以键值对为单位存储
	- key与value用hash算法关联起来，能时候实现时间复杂度为O(1)的查询

- mongodb
	- 以BSON文档为单位存储
		- BSON文档是JSON文档的二进制化
	- 内部引擎由JavaScript实现
	- 把文档存储成BSON结构，在查询的时候将BSON文档转换成js对象，并可以通过js语法来操作 

#### mongodb 与 传统型数据库

- 传统型数据库(sql，比如MySQL)：
	- **结构化数据**，每一行的内容必须要符合定制好的表结构（字段的个数和类型都一样）
	
- mongodb
	- 没有结构，以文档为单位，每一个文档都可以有自己独特的结构（JSON对象都可以拥有自己的属性和值）

> 应用场景： 电影、影评、影评的回复、打分。在传统数据库中，至少至少要四张表，在mongodb中只需要一篇文档就可以完成

```javascript
{
film: "filename"
comment:[
		{content: "评论",
		 reply: "评分"
		}
	]
}
```


#### 数据库文件

- MySQL中，每一个数据库都是一个文件夹，文件夹下的文件，对应着数据库中表的结构和存放的数据
- 在MongoDB中，


#### MongoDB document结构
> 这个结构类似python中的字典

- MongoDB中的一条记录document文档表示，它是由一组键值（集合）对组成，键就是field，值就是存放的数据信息。

#### Mongodb collection集合
- mongodb没有表的概念，它的集合只是用来存放文档，无法限制存放其内文档的结构
