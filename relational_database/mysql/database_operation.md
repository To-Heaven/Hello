# 数据库操作

> 对于某些数据库和表共有的命令关键字，比如select, drop, create, alter等，这些关键字后面一定要加上被操作对象的类型，因此在写这一类命令的时候要指明是database还是table。
> 对于数据库或者表自己独有的关键字命令，就不用声明关键字作用的对象了，直接使用该关键字作用到对应的名称上就好，比如数据库的use方法

## 基本简单操作
- 创建数据库
	- `create database  数据库名`

```sql
mysql> create database db1;
Query OK, 1 row affected (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| db1                |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
6 rows in set (0.00 sec)

mysql>
```

- 修改数据库相关配置
	- `alter database 数据库名  修改操作`

```sql
mysql> show create database db1;
+----------+----------------------------------------------------------------+
| Database | Create Database                                                |
+----------+----------------------------------------------------------------+
| db1      | CREATE DATABASE `db1` /*!40100 DEFAULT CHARACTER SET latin1 */ |
+----------+----------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> alter database db1 charset utf8;
Query OK, 1 row affected (0.00 sec)

mysql> show create database db1;
+----------+--------------------------------------------------------------+
| Database | Create Database                                              |
+----------+--------------------------------------------------------------+
| db1      | CREATE DATABASE `db1` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+----------+--------------------------------------------------------------+
1 row in set (0.00 sec)

mysql>
```

- 查看数据库相关信息
	- `show create database 数据库名`
		- 查看数据库的配置信息
	- `select database()`
		- 查看当前use的数据库
	- `show databases`
		- 查看所有用户权限下可以进入（进入不代表居于该数据库权限，可能只具有该数据库下某张表的权限）的数据库 

```sql
mysql> show create database db1;
+----------+----------------------------------------------------------------+
| Database | Create Database                                                |
+----------+----------------------------------------------------------------+
| db1      | CREATE DATABASE `db1` /*!40100 DEFAULT CHARACTER SET latin1 */ |
+----------+----------------------------------------------------------------+
1 row in set (0.29 sec)
```

- 删除数据库
	- `drop database 数据库名`


```sql
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| db1                |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
6 rows in set (0.00 sec)

mysql> drop database db1;
Query OK, 1 row affected (0.55 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
5 rows in set (0.00 sec)

mysql>
```
