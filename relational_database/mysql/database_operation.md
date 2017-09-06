# 数据库操作

> 对于某些数据库和表共有的命令关键字，比如select, drop, create, alter等，这些关键字后面一定要加上被操作对象的类型，因此在写这一类命令的时候要指明是database还是table。
> 对于数据库或者表自己独有的关键字命令，就不用声明关键字作用的对象了，直接使用该关键字作用到对应的名称上就好，比如数据库的use方法

## 基本简单操作
- 创建数据库
	- **注意，在Linux下，数据库名称是区分大小写的！！！**而在Windows下不区分大小写
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

- 使用数据库
	- `use db_name`
	- 相当于进入了该数据库文件夹内
	- **也可以在调用mysql命令登陆的时候选择数据库**
		- `mysql -h host -u user -p db_name`
		- 不建议把密码放在命令行上

```sql
C:\WINDOWS\system32>mysql -h localhost -u root -p db
Enter password: ****
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 5.7.19 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> select database();
+------------+
| database() |
+------------+
| db         |
+------------+
1 row in set (0.00 sec)

mysql>


```

- 修改数据库相关配置
	- `alter database 数据库名  修改操作`
	- 常用情景
		- 当原有的数据库字段配置不足以满足数据存放的需求时，使用alter来修改相关配置信息，比如字符长度，时间格式等

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
		- `show databases` 不显示没有权限的数据库
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
