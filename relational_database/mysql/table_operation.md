# 表操作

## 基础简单操作
- 创建一个表
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

- 默认情况下，创建好的表是保存在当前使用的数据库下的，使用下面的方法可以将创建好的表保存到指定的任意数据库中（权限足够的情况下）
	- `create table db_name.tb_name(...)`
	
```sql
mysql> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| class                     |
| columns_priv              |
| course                    |
| db                        |
| engine_cost               |
| event                     |
| func                      |
| general_log               |
| gtid_executed             |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| innodb_index_stats        |
| innodb_table_stats        |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| server_cost               |
| servers                   |
| slave_master_info         |
| slave_relay_log_info      |
| slave_worker_info         |
| slow_log                  |
| student                   |
| tables_priv               |
| teacher                   |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
35 rows in set (0.00 sec)
mysql> create table db1.student like student;
Query OK, 0 rows affected (0.32 sec)

mysql> create table db1.teacher like teacher;
Query OK, 0 rows affected (0.36 sec)

mysql> create table db1.class like class;
Query OK, 0 rows affected (0.45 sec)

mysql> create table db1.course like course;
Query OK, 0 rows affected (0.47 sec)


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
- 修改表的编码格式

```python
mysql> show create table student\G;
*************************** 1. row ***************************
       Table: student
Create Table: CREATE TABLE `student` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` char(20) DEFAULT NULL,
  `sex` char(6) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `course` char(10) DEFAULT NULL,
  `class` char(20) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1
1 row in set (0.00 sec)

ERROR:
No query specified

mysql> alter table student
    -> charset utf8;
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>

```

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
		- 这种方法只会拷贝表的结构
		- 这种方法会把主键拷贝

```sql

```

- 方法2
	- `create table 新表名 as select * from 目标表名`
		- key不会复制主键、外键和索引

```sql

```

- 如果指向拷贝目标表的结构
	- `create table 新表名 as select * from 目标表名 where 2=3`
