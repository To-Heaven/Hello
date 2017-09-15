# MySQL数据备份

- 物理备份
	- 直接将`data`路径下的数据库文件拷贝并备份，适用于大型数据库
- 逻辑备份
	- 将数据库或表等其他数据转换成可执行的SQL语句，并保存到一个`.sql`文件中
	- 这样备份的效率较低，适合中小型数据库
- 导出表
	- 使用MySQL提供的命令将指定表按照指定格式保存到文件中 

## 逻辑备份
- 逻辑备份将库及其数据转换成对应的sql语句保存在一个sql文件中。
	- 通过执行sql文件中的sql语句来恢复数据有两种情况
		1. 在命名行中使用mysqldump命令
		2. 在mysql交互环境中使用source命令（使用source需要先选择接收数据的数据库）

- 备份单个数据库

```dos
C:\WINDOWS\system32>mysqldump -uroot -p db > D:\\db.sql
Enter password: ****

C:\WINDOWS\system32>
```

- 恢复单个数据库
	- 恢复之前需要创建一个数据库，用于存放将要导入的数据

```dos
C:\WINDOWS\system32>mysql -u root -p bak <  D:\\db.sql
Enter password: ****

C:\WINDOWS\system32>

---------------------------------------
# 或者
mysql> use bak
Database changed
mysql> source D:\\db.sql;
```

- 备份数据库中指定的表

```dos
C:\WINDOWS\system32>mysqldump -u root -p db2 tb tt > tb_tt.sql
Enter password: ****

C:\WINDOWS\system32>
```

- 恢复表

```sql
C:\WINDOWS\system32>mysql -u root -p db2 <  tb_tt.sql
Enter password: ****
----------------------------------------
mysql> use db2;
Database changed
mysql> source D:\\tb_tt.sql
ERROR:
Failed to open file 'D:\\tb_tt.sql', error: 2
mysql> source tb_tt.sql

```


- 备份多个数据库

```dos
C:\WINDOWS\system32>mysqldump -u root -p --databases db1 db2 > db1_db2.sql
Enter password: ****
```

- 恢复多个数据库
	- 命令行中
		- 不需要创建新的数据库去接收数据，并且命令行**不要再加上**`--databases`
	- MySQL交互解释器中
		- 不需要选择数据库，直接`source + 路径  `导入

```sql
C:\WINDOWS\system32>mysql -u root -p < db1_db2.sql
Enter password: ****
----------------------------------------
mysql> source db1_db2.sql
```


- 备份data目录下所有数据库

```dos
C:\WINDOWS\system32>mysqldump -u root -p --all-databases >all.sql
Enter password: ****
```

- 导入和上面多个数据库操作相同



## 导出表
- 注意：
	- 新版本的MySQL考虑到了数据安全，直接在使用命令形式导出表时不被允许的，使用`set global/session + 配置`也不行
	- 这种情况只能在配置文件中，修改`[mysqld]` section中的对应配置才行，看下面

```sql
mysql> select * from class into outfile 'class_tb.sql'
    -> fields terminated by ','
    -> optionally enclosed by '"'
    -> lines terminated by '\n';
ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement
mysql> show variables like '%secure%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
| secure_auth              | ON    |
| secure_file_priv         | NULL  |
+--------------------------+-------+
3 rows in set, 8 warnings (0.01 sec)

mysql> set session secure_auth=OFF;
ERROR 1229 (HY000): Variable 'secure_auth' is a GLOBAL variable and should be set with SET GLOBAL
mysql> set global secure_auth=OFF;
ERROR 1231 (42000): Variable 'secure_auth' can't be set to the value of 'OFF'
mysql> set global secure_file_priv='D:\\';
ERROR 1238 (HY000): Variable 'secure_file_priv' is a read only variable
mysql> set session secure_file_priv='D:\\';
ERROR 1238 (HY000): Variable 'secure_file_priv' is a read only variable
mysql>
```

>  修改`my.ini`配置文件如下

```
[mysqld]
character_set_server=utf8
secure_file_priv='D:\\'	# 修改了这里

[client]
user=root
password=pass
default-character-set=utf8

[mysql]
default-character-set=utf8
```

> 再次导出到文件

```sql
mysql>  SELECT * FROM db1.class
    -> INTO OUTFILE 'class_tb.txt'
    -> FIELDS TERMINATED BY ','
    -> OPTIONALLY ENCLOSED BY '"'
    -> LINES TERMINATED BY '\n' ;
Query OK, 0 rows affected (0.02 sec)
```

> 从文件中导入到数据库，但是要注意：**先把接收数据的表创建出来，这个表要对备份表的字段**

```sql
mysql> LOAD DATA INFILE 'class_tb.txt' INTO TABLE class
    -> FIELDS TERMINATED BY ','
    -> OPTIONALLY ENCLOSED BY '"'
    -> LINES TERMINATED BY '\n';
```

## 数据库迁移
- 要保证迁移的MySQL版本之间相同，否则会出现兼容性错误
- 格式


```sql
mysqldump  -h 源IP -u 本地用户 -p密码 --databases db1 | mysql -h 目标IP -u 目标用户 -p密码 
```

- 举例，注意要把本地用户密码和目标用户密码都写上，虽然会有警告

```sql
C:\WINDOWS\system32>mysqldump -h localhost -u root -p --databases db1 | mysql -h localhost -u root -p
Enter password: Enter password: ****
mysqldump: Got error: 1045: Access denied for user 'root'@'localhost' (using password: YES) when trying to connect

ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)

C:\WINDOWS\system32>mysqldump -h localhost -u root -p --databases db1 | mysql -h localhost -u root -ppass
Enter password: mysql: [Warning] Using a password on the command line interface can be insecure.

mysqldump: Got error: 1045: Access denied for user 'root'@'localhost' (using password: NO) when trying to connect

C:\WINDOWS\system32>mysqldump -h localhost -u root -ppass --databases db1 | mysql -h localhost -u root -ppass
mysqldump: [Warning] Using a password on the command line interface can be insecure.
mysql: [Warning] Using a password on the command line interface can be insecure.

C:\WINDOWS\system32>
```






