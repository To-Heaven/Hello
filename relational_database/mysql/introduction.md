
[点击查看我整理的本节思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/mysql_introduction.png?raw=true)

## 为什么要使用数据库

- 文件
	1. 如果没有数据库，我们把数据存放在文件中，第一行存放字段信息，其他行存放字段对应的数据信息。多个表就需要创建多个文件，如果这些表之间有共同的关系关联着，比如相同的字段id等等，那么就可以创建一个文件夹，将这些有联系的文件存放在这个文件夹中。
	2. 上述这种存放数据的方式完全可以达到我们想要的效果——我们可以在需要获取表中某字段对应数据的时候把文件内容读取到内存中，然后再通过一系列操作来获取想要的数据。
	3. 但是问题来了，每次操作从文件中获取数据的时候都要经过读取文件、处理数据，筛选数据等操作，显然降低了工作效率。
	4. 于是，你想到用函数将查询文件中对应字段的功能封装起来，这样确实可以避免重复造轮子，但是新的问题又出现了。
	5. 这些文件存放在一台主机上，并且是由电脑上对应的程序来进行读、写和查询等操作的，如果其他电脑上的程序也想访问这个文件，操作数据库呢？
	6. 这时，我们可以创建基于socket套接字的服务端和客户端，将增删改查及其他更多的功能封装成一个个功能接口保存到客户端中，从而建立服务端与客户端之间的通讯，实现客户端远程对服务端主机的文件中的表信息进行操作
	7. 但是如果客户端越来愈多，那么就需要在每一台客户端主机上建立一个socket套接字客户端，并将对应的功能封装到其中，这样重复的造轮子显然不是我们想要的
	8. 另外，多个客户端共享同一台主机上的文件资源一定会产生文件资源的竞争使用，这样绝对不利于文件以及文件中数据的安全，因此**需要将客户端对服务端文件操作的过程进行加锁**，保证文件及数据的安全

> 数据其实就是一条条的记录，它存放被描述对象的多个特征，而文件中的每一行内同都对应着每一条记录



- 数据库
	- 数据库就帮我们完成了上述的操作，对应上述过程：
		- 数据库同样有客户端和服务端
		- 数据库客户端和服务端之间的通讯同样是基于socket套接字来完成了
		- 每一个通过客户端创建的数据库，在客户端指定的路径下被保存成了一个个的文件夹，而该数据库中对应的一各个数据表就以文件的格式存放在该文件夹下
			- __一个数据库其实就是一个文件夹__
			- __一个表就是文件夹下对应的文件__
		- 数据库中的数据是安全的，因为数据库帮助我们实现了加锁的过程 

- 数据库的优点
	- 程序员只需要关心代码逻辑，不需要关心客户端服务端的构建和数据的安全（加锁）
	- 解决了从文件处理数据，加锁等问题


## 关系型数据库和非关系型数据库
- 简单来说，关系型数据库是由一个个关联的表组成的
- 非关系型数据库则没有表的概念 

#### 关系型数据库的特点
1. **表结构固定**
2. **使用关系模型组织数据，使数据之间的耦合性较强**
3. **降低的数据的冗余**
4. **事务的一致性**

#### 关系型数据库的缺点
1. 表结构固定导致只有固定结构的数据才能存放在数据表中
2. 读写性能差
	- 跨表查询总会产生性能损耗
3. 较难满足高并发读写需求

## 数据库配置

#### 数据库初始化
> 确保将MySQL的bin目录下文件的路径添加到系统环境变量中

- 初始化数据库用来干什么？
	- 刚安装好的数据库服务器是没有`data`目录的，`data`目录起始就是用来存放数据库的目录，之后在初始化数据库之后我们才能创建一个数据库。

- 什么时候使用数据库初始化
	1. 刚安装好MySQL，需要创建第一个数据库的时候
	2. 修改了配置文件中的datadir对应路径值之后

- 配置文件在哪儿？
	- Linux中，MySQL的配置文件路径一般为`/etc/my.cnf`
	- 而在Windows中，我们可以讲配置文件放在MySQL的安装根目录下

- 如何初始化？
	- `mysqld --initialize`
		- 此操作之后会在配置文件`my.cnf`中datadir指定的路径下创建一个data目录，该目录下的每一个文件夹都对应着给一个数据库
		- 这种初始化方式会默认给root用户分配一个随机密码。保存在MySQL的日志文件`/var/log/mysql.log`中。
	- `mysqld --initialize-insecure` 
		- 这种方法可以不让MySQL默认为root用户创建随机密码，这样初始化数据库之后，使用`mysqladmin -u root password 密码`来创建密码
  

## 用户名管理


###### 创建新用户方式一
- 需要登录到客户端的root用户在mysql中创建
- 命令如下
	- `localhost`表示创建的用户只能在本地主机中登陆
	- `%`表示创建的用户可以在互联网的其他主机上登陆到本地的数据库

```mysql
create user 'ziawang' @ 'localhost' identified by 'password'
create user 'wangzihao' @ '%' identified by 'password'
```

###### 方式二
- 也可以在授予权限的同时创建用户

```
grant all on *.* to 'user'@'%' identified by 'password'
```


## 密码管理

#### 创建root密码(mysqld --initialize-insecure)
- 如果你使用的是`mysqld --initialize-insecure`方法初始化的数据库，那么你可以使用下面命令创建root用户密码

```mysql
mysqladmin -u root password 密码
```

#### 初始密码
- MySQL5.7中会给root用户随机生成一个密码，我们可以在`error log`中找到，如果你是使用rpm安装的，那么这个文件的位置就是`/var/log/mysqld.log`

- 整个步骤如下
	1. 使用`grep "password" /var/log/mysqld.log`获取MySQL生成的随机密码
	2. 使用该密码登陆root用户
	3. 登陆完成之后必须立刻修改密码，不然你干什么操作都会报错
		- `ALTER USER user() identified by 'newpassword'`
	4. 新创建的密码也是有规范的，MySQL中有三种密码的规范，我们可以使用`set global validate_password_policy=0|1|2`来设置
		- 0 or LOW：length，支队长度有限制
		- 1 or MEDIUM： 默认为1， 必须符合长度，且必须含有数字，小写或大写字母，特殊字符 
		- 2 or STRONG： 必须符合长度，且必须含有数字，小写或大写字母，特殊字符，还要有一个dictionary file

- 配置密码参数
	- `show variables like 'validate_password%'`
		- 此命令会列出密码规则的相关配置，我们可以使用`set global 来修改`
	- 如果你只想查看其中一个配置参数的值，使用`select`
		- `select @@validate_password_mixed_case_count`
 

```
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password_dictionary_file    |       |
| validate_password_length             | 8     |
| validate_password_mixed_case_count   | 2     |
| validate_password_number_count       | 1     |
| validate_password_policy             | LOW   |
| validate_password_special_char_count | 1     |
+--------------------------------------+-------+
6 rows in set (0.00 sec)

mysql> set global validate_password_mixed_case_count=2;
Query OK, 0 rows affected (0.00 sec)
```

#### 忘记密码（破解密码）
> mysql底层仍然使用的是在socket套接字通信  

-  客户端登陆验证的密码是保存在服务端主机的文件中的。同样的，在MySQL中，客户端的验证信息也是以文件的方式保存的
-  方法一：
	1. 如果MySQL服务端还在运行，要先关闭mysqld，记得以什么方式打开就以什么方式关闭
	2. 输入命令   `mysqld --skip-grant-tables`，这样就不用密码直接输入mysql就可以登陆root账户
		1. 此命令无效时，使用下面命令`mysqld_safe --user=mysql --skip-grant-tables --skip-networking & mysql -u root mysql`，执行此命令之前要关闭mysqld
	3. 进入之后使用命令
		- 在5.7版本的MySQL中    `update mysql.user set authentication_string=password('密码') where user='root' and host='localhost'`   注意使用引号
		- 在5.7版本之前的MySQL中    `update mysql.user password=password('密码') where user='root' and host='localhost'`
	4. 使用`flush privileges` 刷新权限
	5. 关闭服务端
		- Linux中
			- `service mysqld stop`
			- 或者使用`pkill -9 PID`杀死该进程
		- Windows中使用`tskill mysqld` 
			- 如果这个命令不能用，使用这个   `taskkill /T /F /PID 进程号`   
				- /T 表示将PID对应的进程和子进程都kill
				- /F 标志强制关闭
				- /PID  即进程id   可以使用`tasklis | findstr`查看 
	6. 使用新设置的root密码等登陆




- 方法二
	1. 新创建一个配置文件
		- Linux可以在`/etc/my.cnf`
		- Windows可以选择在安装根目录下创建`my.ini`配置文件
	2. 在配置文件中按照格式写入配置，如下
		- cnf配置文件的注释在使用井号"#"
		- ini配置文件的注释使用井号"#"或分号	
	3. 由于datadir即数据库路径变化了，因此需要对数据库进行初始化 `mysqld --initialize-insecure`
		- 这样初始化之后，原先的密码就不存在了，这样就可以重新创建密码了

```
[mysqld]
skip-grant-tables
# mysql安装目录
basedir=D:\mysql
; 数据库的路径
datadir=D:\data
# 端口，默认为3306
port=3306
# 编码，注意utf8不是utf-8，如果是
character_set_server=utf8
host=localhost
```

#### 使用user()
- 在MySQL中，`user()`返回的是当前登陆的对象。在修改密码和赋予权限定额过程中，你可以使用这个函数，当然也可以直接用字符串指定用户


#### 注意
- **mysql 在使用"mysql_safe"重置root密码并登录之后，必须马上修改密码，不然会报错**
	- `ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';`
	- 或者使用user()  `alter user user() identified by '新密码'`


## 验证登陆
- 刚创建的MySQL数据库，用户在进行使用的时候是没有设置密码的，因此只需要在客户端终端这样
	- `mysql -u root`
- 对于一个存在了用户名和密码的数据库，就要这样访问
	- `mysql -u username -p`
- 完整的登陆因该是这样
	- `mysql -h host -u username -p `
		- host: MySQL服务器正在运行的主机名称


## 将MySQL服务端添加到系统服务列表中（windows下）
- 为了方便，在使用时可以将MySQL服务端启动任务添加到系统服务列表中
- Windows下步骤这样
	- 命令行下  `mysqld  --install`		
		- 管理员身份开启命令行
	- 打开运行小窗口 `win + R`
	- 输入 `services.msc`
	- 找到MySQL，点击启动，服务端就启动了
		- 也可以将其设置成自动启动模式

## 端口
- MySQL的默认端口是3306
- 修改端口
	- 在`my.ini`文件中修改port对应的值
	- 如  `port=8888`
	



## 启动和关闭MySQL服务端

#### linux
- 启动:
	1. `service mysqld start`

- 关闭:
	1. `service mysqld stop`

#### windows

- 启动：
	1. 终端命令行下输入`mysqld` ，服务端就跑起来了，这个时候，该命令行会卡在该位置，关掉该窗口并不会导致MySQL服务端关闭
	2. Windows下，将服务端添加到系统服务列表中启动
	3. `net start mysql`

- 关闭：
	- **用上述什么方式打开的就用什么方式关闭**
	1. 对于上述第一种，要用下面这种方法关闭
		-  `tskill mysqld`
		-  注意，使用这种方法应先检查任务是否是在运行   `tashlist  | findstr mysql`
	2. 对于第二种，直接手动在服务列表中停用服务器
	3. `net stop mysql`




## 事务
- 特点：多个连续的任务同时成功或同时失败
	- 只要有一个任务运行失败，那么所有的任务都会运行失败
	- 比如银行转账包括三个任务，对目标账户转账、本地账户金额减少和目标账户金额增加，只要有一个任务失败，所有的任务不会成功，并且会发生回滚
		- 回滚就是当事务中间某个任务失败的时候，其他已经完成的任务会退回到任务开始之前的状态
 

## 创建新用户
- **root用户下才能创建其他用户**
- 创建一个只能在本地主机登陆的账户

```sql
mysql> select user();
+----------------+
| user()         |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)

mysql> create user 'local_username'@'local' identified by 'password';
Query OK, 0 rows affected (0.00 sec)
```

- 创建一个可以在任何客户端上登陆的用户名，不过登陆的时候需要提供目标服务端地址

```sql
mysql> create user 'username'@'%' identified by 'pass';
Query OK, 0 rows affected (0.00 sec)

mysql> quit
Bye

C:\WINDOWS\system32>mysql -p192.168.20.28 -u username -p
mysql: [Warning] Using a password on the command line interface can be insecure.
Enter password: ****
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 5.7.19 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## MySQL用户权限管理
- 语法格式如下

```sql
grant
priv_type [(column_list)]
      [, priv_type [(column_list)]] ...
    ON [object_type] priv_level
    TO user [auth_option] [, user [auth_option]] ...
    [REQUIRE {NONE | tls_option [[AND] tls_option] ...}]
    [WITH {GRANT OPTION | resource_option} ...]
```

- 权限的级别
	- `*.*`  
		- 针对所有库的授权
		- 第一个`*`代表所有数据库，第二个`*`代表所有表
	- `db_name.*` 
		- 针对某一数据库的授权
		- 即指定数据库db_name下的所有表
	- `db_name.table_name`
		- 针对某一数据库下指定表的授权
		- 即指定数据库下指定的表 
	- `priv_type (column1， column2. . .) on priv_level `
		- 对 priv-level上的指定字段进行授权

- 举例为新用户 ziawang 分别赋予不同级别的权限 
	- 为ziawang授权在db_1数据库下table_1表中字段id和name的查询操作，以及对name字段的修改操作 

```sql
mysql> grant
    -> select(id, name), update(name)
    -> on db_1.table_1 to ziawang@'localhost' identified by 'pass';
```

- 如果要把指定的数据库、表或字段的所有命令权限分配给用户，使用`all`来代替长长的语句
```sql
mysql> create user 
mysql> ziawang@'localhost' 
mysql> identified by 'pass';
Query OK, 0 rows affected (0.06 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| db                 |
| db1                |
| db2                |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
8 rows in set (0.00 sec)

mysql> grant all 
mysql> on db.* 
mysql> to ziawang@'localhost';
Query OK, 0 rows affected (0.29 sec)
```

> 这里如果用户不存在，可以直接通过auth_option创建用户，并绑定密码

- 注意，此时ziawang登陆客户端之后，能看到的数据库 只有两个，同理，表只有一个t_1

```sql
information_schema
t_1
```


## root用户初始化的库
- information_schema
	- 虚拟库，不占用磁盘空间，存储的是数据库启动后的一些参数，如用户表信息、列信息、权限信息、字符信息
- performance_schema
	- 用于收集数据库服务器性能参数，记录处理查询请求时发生的各种事件、锁等现象
-   mysql
	-   授权库，主要存储系统用户的权限信息
-    test
	-    	MySQL数据库系统自动创建的测试数据库

## 查看指定用户的权限信息
- 授权表
	- 存放给了所有用户的权限信息
		- user
		- db
		- tables_priv
		- columns_priv
	- `show grants for user@'host'` 

```sql
mysql> show grants for ziawang@'localhost';
+---------------------------------------------------------+
| Grants for ziawang@localhost                            |
+---------------------------------------------------------+
| GRANT USAGE ON *.* TO 'ziawang'@'localhost'             |
| GRANT ALL PRIVILEGES ON `db`.* TO 'ziawang'@'localhost' |
+---------------------------------------------------------+
2 rows in set (0.00 sec)

mysql>
```

## 查看当前登陆的用户
- `select user();`

```
mysql> select user();
+-------------------+
| user()            |
+-------------------+
| ziawang@localhost |
+-------------------+
1 row in set (0.00 sec)

mysql> 
```

## 删除用户
- `drop user username@'host'`

```sql
mysql> drop user ziawang@'localhost';
Query OK, 0 rows affected (0.03 sec)

mysql> show grants for ziawang@'localhost';
ERROR 1141 (42000): There is no such grant defined for user 'ziawang' on host 'localhost'
mysql>
```

## 用户重命名
- `rename user 旧名@'host' to  新名@'host'`

```sql
mysql> rename user ziawang@'localhost' to wangziahao@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql>
```



## 配置文件
- Linux下，配置文件路径为`/etc/my.cnf`
- 常用的sections
	- [mysqld]
		- mysqld section下存放着服务端启动时的配置，常用的配置信息如下
			- `# skip-grant-tables`
				- 取消注释时，直接输入mysql就可以以root身份进入mysql，常用于密码的设置和破解
			- character_set_server
				- 5.7版本中对编码格式的设置与之前版本不同
				- 旧版`default-character-set`  注意不是下划线，在新版中是下划线
			- port=3306
				- 端口号，可以修改
			- basedir
				- 客户端解压的目录
			- datadir
				- 数据库保存的路径，第一次安装初始化的时候，会在该路径下创建一个data文件夹，用于存放用户创建的数据库
			- default-storage-engine
				- 创建新表时将使用的默认存储引擎（常委InnoDB） 
			- max_connections
				- MySQL拂去其支持的最大的并发连接数 
	- [client]
		- 客户端命令的全局配置
		- 当[mysql]section中部分设置没有配置的时候，启动客户端时会调用[client]中相关的配置信息
		- 常用配置信息如下
			- user
				- 在输入`mysql`时，如果在[mysql]section中没有指定user，那么直接[client]section中的用户，前提是该用户已经被创建，并且配置信息中的密码是正确密码
			- password
				- 用于提供上述user的密码
			- default-character-set
				-  客户端的编码搁置
			- port
	- [mysql]
		- 登陆时调用的配置文件，是客户端的局部配置
		- 配置信息同[client]section，相同的配置参数会覆盖[client]中对应的配置参数


## MySQL终端操作
- 输入命令格式
	- 一个查询不要全部在一行
		- MySQL通过查找终止分号来确定命令的结尾

```sql

mysql> select name, age from t
    -> where
    -> id = 2;
```


- '\c'   
	- \c 用于输入错SQL语句时使用，就会结束当前语句输入，直接开启新命令行
	- 由SQL语句中的引号是成对出现的，因此遇到这种情况，要先补全引号再使用 \c

```sql

mysql> create database charse
    -> \c
mysql> create database charset 'utf8
    '> '\c
mysql>
```



## 在命令行界面执行SQL语句
- `mysql -u 用户名 -p 密码 -e 'SQL语句'`
	- 注意，在Windows系统命令行中，要sql语句要用双引号，在Linux中是单引号

```sql
C:\WINDOWS\system32>mysql -u root -p -e "use db1; show tables;"
Enter password: ****
+---------------+
| Tables_in_db1 |
+---------------+
| course        |
| student       |
| teacher       |
+---------------+

C:\WINDOWS\system32>
```

