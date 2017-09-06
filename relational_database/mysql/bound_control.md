# 约束条件

##  primary key
- 将指定字段设定为主键
- 主键特点
	- not null
	- unique
- 应用
	- 设置外键时，被关联的表字段的值必须是唯一的，因此将该字段设置为主键
	- 常用于id字段 
- 一个表中只能有一个主键
	- 一个字段组成主键（单列主键）
	- 多个字段组成主键（多列主键）
		- 使用    primary key(field1, field2)

```sql
# 单列主键
mysql> create table t5(
    -> id int primary key auto_increment,
    -> name char(20),
    -> age int
    -> );
Query OK, 0 rows affected (0.28 sec)

mysql> desc t5;
+-------+----------+------+-----+---------+----------------+
| Field | Type     | Null | Key | Default | Extra          |
+-------+----------+------+-----+---------+----------------+
| id    | int(11)  | NO   | PRI | NULL    | auto_increment |
| name  | char(20) | YES  |     | NULL    |                |
| age   | int(11)  | YES  |     | NULL    |                |
+-------+----------+------+-----+---------+----------------+
3 rows in set (0.00 sec)

mysql>

```


```sql
# 多列主键
mysql> create table services(
    -> ip char(16),
    -> port int,
    -> primary key(ip, port)
    -> );
Query OK, 0 rows affected (0.49 sec)

mysql> desc services;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| ip    | char(16) | NO   | PRI | NULL    |       |
| port  | int(11)  | NO   | PRI | NULL    |       |
+-------+----------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql>

``` 


##  foreign key 外键


- 优点
	- 节省空间 

- 主键表/外键表
	- 主键表：包含被关联主键的表
	- 外键表：关联了其他表的主键的表

- 主键表更新/删除
	- `on update` 和 `on delete`
		- 后面可以跟的词语有四个
			- `cascade` 
				- 主键表中被参考字段更新，外键表中也更新，主键表中的记录被删除，外键表中改行也相应删除
			- `no action `
				- 不做任何操作
			- `set null`
				- 在外键表中将对应的字段设置为`null`
			- `set default`
				- 设置为默认值 

- 注意：
	- 先创建被关联的表
		- 对应的字段必须满足`unique`约束（使用主键）


```sql
mysql> create table company(
    -> id int primary key auto_increment,
    -> name char(20),
    -> money int
    -> );
Query OK, 0 rows affected (0.31 sec)

mysql> desc company;
+-------+----------+------+-----+---------+----------------+
| Field | Type     | Null | Key | Default | Extra          |
+-------+----------+------+-----+---------+----------------+
| id    | int(11)  | NO   | PRI | NULL    | auto_increment |
| name  | char(20) | YES  |     | NULL    |                |
| money | int(11)  | YES  |     | NULL    |                |
+-------+----------+------+-----+---------+----------------+
3 rows in set (0.00 sec)

mysql> create table employ(
    -> id int ,
    -> name char(20),
    -> com_id int,
    -> constraint emp_com foreign key(com_id)
    -> references company(id)
    -> on update cascade
    -> on delete cascade
    -> );
Query OK, 0 rows affected (0.35 sec)

mysql> desc employ;
+--------+----------+------+-----+---------+-------+
| Field  | Type     | Null | Key | Default | Extra |
+--------+----------+------+-----+---------+-------+
| id     | int(11)  | YES  |     | NULL    |       |
| name   | char(20) | YES  |     | NULL    |       |
| com_id | int(11)  | YES  | MUL | NULL    |       |
+--------+----------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> insert into company values
    -> (1, 'a', 100),
    -> (2, 'b', 200),
    -> (3, 'c', 300);
Query OK, 3 rows affected (0.04 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> insert into employ values
    -> (1, 'ziawang', 1),
    -> (2, 'haha', 2),
    -> (3, 'xixi', 3);
Query OK, 3 rows affected (0.11 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> select * from company;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |   100 |
|  2 | b    |   200 |
|  3 | c    |   300 |
+----+------+-------+
3 rows in set (0.00 sec)

mysql> select * from employ;
+------+---------+--------+
| id   | name    | com_id |
+------+---------+--------+
|    1 | ziawang |      1 |
|    2 | haha    |      2 |
|    3 | xixi    |      3 |
+------+---------+--------+
3 rows in set (0.00 sec)

mysql> update company
    -> set
    -> id = 5
    -> where \c
mysql> update company set
    -> id = 5
    -> where name = 'b';
Query OK, 1 row affected (0.08 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from company;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |   100 |
|  3 | c    |   300 |
|  5 | b    |   200 |
+----+------+-------+
3 rows in set (0.00 sec)

mysql> select * from employ;
+------+---------+--------+
| id   | name    | com_id |
+------+---------+--------+
|    1 | ziawang |      1 |
|    2 | haha    |      5 |
|    3 | xixi    |      3 |
+------+---------+--------+
3 rows in set (0.00 sec)

mysql>

```




## auto_increment
- 偏移量`auto_increment_offset`
	- 要自增的字段需要在字段中声明 `auto_incremennt`
	- 设置偏移量	
		1. 创建完之后，使用`alter table tb_name auto_increment=偏移值`
		2.  values之后设置auto_increment的起始值`auto_increment=n`
		3.  `set session auto_increment_offset = 偏移量`
		4.  `set global auto_increment_offset = 偏移量`
	- 使用`show create  table tb_name`可以查看`auto_increment`的值在自增过程中的变化

```sql

mysql> create table t10(
    -> id int primary key auto_increment,
    -> name char(10)
    -> ) auto_increment=5;
Query OK, 0 rows affected (0.30 sec)

mysql> show create  table t10\G;
*************************** 1. row ***************************
       Table: t10
Create Table: CREATE TABLE `t10` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` char(10) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=latin1
1 row in set (0.00 sec)

ERROR:
No query specified

mysql> insert into t10(name) values
    -> ('ziawang'),
    -> ('haha');
Query OK, 2 rows affected (0.04 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> select * from t10;
+----+---------+
| id | name    |
+----+---------+
|  5 | ziawang |
|  6 | haha    |
+----+---------+
2 rows in set (0.00 sec)

mysql> show create table t10\G;
*************************** 1. row ***************************
       Table: t10
Create Table: CREATE TABLE `t10` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` char(10) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=latin1
1 row in set (0.00 sec)

ERROR:
No query specified

mysql>
```

- 步长 `auto_increment_increment`
	- 在创建表之前声明步长
		- `set session auto_increment_increment`
			- 只设置当前客户端会话中的步长
		- `set global auto_increment_increment`
			- 设置全局（所有）客户端会话的步长
	- 同理，偏移量也可以设置
		- `set session auto_increment_offset`
		- `set global auto_increment_offset`

- **注意**：
	- 如果auto_increment_offset的值大于auto_increment_increment的值，则auto_increment_offset的值会被忽略 
	- 如果删除了表中的所有记录
		- 使用`delete`
			- 再次插入的记录，由于其`auto_increment`的值没有恢复到1，因此会从删除前的id继续往下自增
		- 使用`truncate`
			- id会从1开始，因为`auto_increment`值被此方法删除后重新设置成了1 
	- 使用`show variables like '%auto_incr%'`来查看对应变量的值
	 


- not null
	- 设定插入到指定字段的值不能为空

- default
	- 创建列时可以指定默认值，当插入数据时如果未主动设置，则自动添加默认值
	- 如果没有设置default值，MySQL会自动设定默认值为  `null`

```sql
mysql> create table t10(
    -> name char(10) not null,
    -> age int default 20,
    -> hobbies char(20)
    -> );
Query OK, 0 rows affected (0.27 sec)

mysql> desc t10;
+---------+----------+------+-----+---------+-------+
| Field   | Type     | Null | Key | Default | Extra |
+---------+----------+------+-----+---------+-------+
| name    | char(10) | NO   |     | NULL    |       |
| age     | int(11)  | YES  |     | 20      |       |
| hobbies | char(20) | YES  |     | NULL    |       |
+---------+----------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql>


```



- unique key
	- `not null unique `   --> `primary key` 
- 单列唯一
	- 设定单个字段插入的值不能重复
- 多列唯一
	- 保证多个字段插入的值的组合不重复
- 为unique key 创建键名
	- `constraint name unique(field1, field2)`

```sql
# 单列唯一
mysql> create table tt(
    -> name char(20) unique
    -> );
Query OK, 0 rows affected (0.30 sec)

mysql> insert tt values
    -> ('ziawang');
Query OK, 1 row affected (0.05 sec)

mysql> insert tt values
    -> ('ziawang');
ERROR 1062 (23000): Duplicate entry 'ziawang' for key 'name'
mysql>

--------------------------------------------------------------------------------
mysql> create table ttt(
    -> name char(20),
    -> constraint uq_name unique(name)
    -> );
Query OK, 0 rows affected (0.44 sec)

mysql> show create table ttt\G;
*************************** 1. row ***************************
       Table: ttt
Create Table: CREATE TABLE `ttt` (
  `name` char(20) DEFAULT NULL,
  UNIQUE KEY `uq_name` (`name`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1
1 row in set (0.00 sec)

ERROR:
No query specified

mysql>
```

```sql
# 多列唯一
mysql> create table srvices(
    -> ip char(16),
    -> port int
    -> ,
    -> constraint ip_port unique(ip, port)
    -> );
Query OK, 0 rows affected (0.31 sec)



mysql> show create table srvices\G;
*************************** 1. row ***************************
       Table: srvices
Create Table: CREATE TABLE `srvices` (
  `ip` char(16) DEFAULT NULL,
  `port` int(11) DEFAULT NULL,
  UNIQUE KEY `ip_port` (`ip`,`port`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1
1 row in set (0.00 sec)

ERROR:
No query specified

mysql> insert into srvices values
    -> ('127.0.0.1', 8080);
Query OK, 1 row affected (0.05 sec)

mysql> insert into srvices values
    -> ('127.0.0.1', 8080);
ERROR 1062 (23000): Duplicate entry '127.0.0.1-8080' for key 'ip_port'
mysql>
```







- unsigned
	- 如果列表中已经有带符号的数值，alter改成unsigned的时候会报错

```sql
mysql> create table t(
    -> class_num int unsigned
    -> );
Query OK, 0 rows affected (0.29 sec)

mysql> insert into t values
    -> (101);
Query OK, 1 row affected (0.07 sec)

mysql> select * from t;
+-----------+
| class_num |
+-----------+
|       101 |
+-----------+
1 row in set (0.00 sec)

mysql> insert into t values
    -> (-101);
ERROR 1264 (22003): Out of range value for column 'class_num' at row 1


```




- zerofill 
	- 使用0填充剩余的宽度

```sql
mysql> create table t(
    -> class_num int(5),
    -> stu_num int(5) zerofill
    -> );
Query OK, 0 rows affected (0.27 sec)

mysql> insert into t values
    -> (101, 1);
Query OK, 1 row affected (0.05 sec)

mysql> select * from t;
+-----------+---------+
| class_num | stu_num |
+-----------+---------+
|       101 |   00001 |
+-----------+---------+
1 row in set (0.00 sec)

mysql>

```











\* 是以匹配的模式去查找，慢
应该将所有字段都写出来
