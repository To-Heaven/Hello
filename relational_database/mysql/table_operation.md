# 表操作
[点击查看我整理的本节思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/table_operation.png?raw=true)

## 基础简单操作
- 创建一个表
	- **注意，在Linux下，数据库名称是区分大小写的！！！**
	- 注意
		1. 创建的表默认使用InnoDB引擎
		2. 用户必须有创建表的权限
		3. 同一张表中字段名不能相同
		4. 字段名和类型是必选参数
	- 格式

```sql
create    [temporary]    table    表名   (
	字段名    类型    [(宽度)   约束条件]，
	字段名    类型    [(宽度)   约束条件]，
	字段名    类型    [(宽度)   约束条件]);
# 注意，最后一个字段名及其类型的创建结束后，不能再跟着一个逗号','，不然会报错
```

```sql
# temporary table，服务端重启之后消失
mysql> use db1
Database changed
mysql> create temporary table t2(
    -> id int,
    -> name char(10));
Query OK, 0 rows affected (0.00 sec)
```

```sql
mysql> create table t1(
    -> id int,
    -> name char);
Query OK, 0 rows affected (0.55 sec)
```


- 查看表信息
	- `show tables`
	- `show create table 表名`   
		- 使用   \G   可以更可视化
	- `describe 表名`
	- `desc 表名`  

```sql
mysql> show tables
    -> ;
+---------------+
| Tables_in_db1 |
+---------------+
| t1            |
+---------------+
1 row in set (0.00 sec)

mysql> show create table t1;
+-------+---------------------------------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                                        |
+-------+---------------------------------------------------------------------------------------------------------------------+
| t1    | CREATE TABLE `t1` (
  `id` int(11) DEFAULT NULL,
  `name` char(1) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+-------+---------------------------------------------------------------------------------------------------------------------+
1 row in set (0.03 sec)

mysql> show create table t1\G;
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `id` int(11) DEFAULT NULL,
  `name` char(1) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)

ERROR:
No query specified
mysql> desc t1;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | YES  |     | NULL    |       |
| name  | char(1) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

- 修改表
	- 修改表名 `alter table 表名 rename 新表名`
	- 修改字段
		- 增加字段 __add__
			- `alter table 表名 add 字段名 数据类型  [完整性约束条件];`
			- `alter table 表名 add 字段名 数据类型  [完整性约束条件] first;`
			- `alter table 表名 add 字段名 数据类型  [完整性约束条件] after 字段名;`
		- 删除字段 __drop__
			- `alter table 表名 drop 字段名;`
		- 修改字段 __modify__
			-  `alter table 表名 modify 字段名 数据类型 [完整性约束条件];`
			-  `alter table 表名 change 旧字段名 新字段名 旧数据类型 [完整性约束条件] ;`
				-  等价  `alter table 表名 rename 新表名`
			- `alter table 表名 change 旧字段名  新字段名  新数据类型 [完整性约束条件]`  

```sql
mysql> show tables;
+---------------+
| Tables_in_db1 |
+---------------+
| tt            |
+---------------+
1 row in set (0.00 sec)

mysql> desc tt;
+-------+-----------+------+-----+---------+-------+
| Field | Type      | Null | Key | Default | Extra |
+-------+-----------+------+-----+---------+-------+
| id    | int(11)   | YES  |     | NULL    |       |
| name  | char(128) | YES  |     | NULL    |       |
+-------+-----------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> alter table tt add phone int first;
Query OK, 0 rows affected (0.60 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc tt;
+-------+-----------+------+-----+---------+-------+
| Field | Type      | Null | Key | Default | Extra |
+-------+-----------+------+-----+---------+-------+
| phone | int(11)   | YES  |     | NULL    |       |
| id    | int(11)   | YES  |     | NULL    |       |
| name  | char(128) | YES  |     | NULL    |       |
+-------+-----------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> alter table tt drop phone;
Query OK, 0 rows affected (0.59 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc tt;
+-------+-----------+------+-----+---------+-------+
| Field | Type      | Null | Key | Default | Extra |
+-------+-----------+------+-----+---------+-------+
| id    | int(11)   | YES  |     | NULL    |       |
| name  | char(128) | YES  |     | NULL    |       |
+-------+-----------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql>
```


- 删除表
	- `drop table 表名`

```sql
mysql> show tables;
+---------------+
| Tables_in_db1 |
+---------------+
| tt            |
+---------------+
1 row in set (0.00 sec)

mysql> drop table tt;
Query OK, 0 rows affected (0.18 sec)

mysql> show tables;
Empty set (0.00 sec)

mysql>
```


## 主键
1. 不能为空
2. 唯一


## 表字段操作
- 修改存储引擎

```sql
mysql> use db1
Database changed
mysql> alter table t
    -> engine=memory;
Query OK, 0 rows affected (0.39 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>
```

- 添加字段

```sql
mysql> alter table t
    -> add friend char(50);
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>
```

- 删除字段

```sql
mysql> alter table t
    -> drop friend;
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>
```

- 修改字段类型

```sql
mysql> alter table t
    -> modify id int not null primary key;
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>
```

- 增加主键

```sql
mysql> alter table t
    -> modify id int not null primary key;
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>
```

- 增加主键和自动增长

```sql
mysql> alter table t
    -> add id int primary key auto_increment;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> fesc t;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'fesc t' at line 1
mysql> desc t;
+-------+---------+------+-----+---------+----------------+
| Field | Type    | Null | Key | Default | Extra          |
+-------+---------+------+-----+---------+----------------+
| name  | char(5) | YES  |     | NULL    |                |
| age   | int(11) | YES  |     | NULL    |                |
| phone | int(11) | YES  |     | NULL    |                |
| id    | int(11) | NO   | PRI | NULL    | auto_increment |
+-------+---------+------+-----+---------+----------------+
4 rows in set (0.00 sec)

mysql>
```

- 删除主键
- 



## 复制表
- 方法1
	- `create table 新表名  like 目标表名`

```sql

```

- 方法2
	- `create table 新表名 as select * from 目标表名`
		- key不会复制主键、外键和索引

```sql

```

- 如果指向拷贝目标表的结构
	- `create table 新表明 as select * from 目标表名 where 2=3`


## 从文本文件中导入表
- 文本文件的格式
	1. 每一个字段对应的值用制表符隔开
	2. 对于值为NULL的字段值，在文本文件中用`\N`表示
	3. 不同的操作系统，由于换行符的不同，命令也不同
		- linux  --->  `load data local infile '/path/tb.txt' into table tb;`
		- windows  --->  `load data local infile '/path/tb.txt' into table tb  lines terminated by '\r\n'`
		- os x  --->  'load data local infile '/path/tb.txt' into table tb lines terminated by '\r'' 