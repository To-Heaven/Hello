# PostgreSQL表操作

## 创建一个表
- 创建表的操作需要自psql数据库界面下运行，语法和MySQL基本相同

```
CREATE TABLE table_name (
    column_name1 datatype,
    ... ...
    column_nameN datatype,
    PRIMARY KEY( one or more columns ) 
);
```

#### 注意
- **创建表的时候不要用内置关键字作为表名**

```sql
ziawang=> create table user(
ziawang(> name varchar(20),
ziawang(> host char(32),
ziawang(> primary key(name, host));
ERROR:  syntax error at or near "user"
LINE 1: create table user(
                     ^
ziawang=> create table userinfo(
name varchar(20),
host char(32),
primary key(name, host));
CREATE TABLE
ziawang=> \d
          List of relations
 Schema |   Name   | Type  |  Owner  
--------+----------+-------+---------
 public | userinfo | table | ziawang
(1 row)

ziawang=> \d userinfo
          Table "public.userinfo"
 Column |         Type          | Modifiers 
--------+-----------------------+-----------
 name   | character varying(20) | not null
 host   | character(32)         | not null
Indexes:
    "userinfo_pkey" PRIMARY KEY, btree (name, host)

ziawang=> 
```

## 删除表
- 删除表操作是在数据库中进行的，首先要使用`psql`进入数据库操作界面，再使用`drop table table_name`删除指定表

```

```

- **删除表的操作中，删除表的用户必须是表的拥有者**

```
[ziawang@host ~]$ psql
psql (9.5.10)
Type "help" for help.

ziawang=> \d
          List of relations
 Schema |   Name   | Type  |  Owner   
--------+----------+-------+----------
 public | tb       | table | postgres
 public | userinfo | table | ziawang
(2 rows)

ziawang=> drop table tb;
ERROR:  must be owner of relation tb
ziawang=> drop table userinfo;
DROP TABLE
ziawang=> \d
        List of relations
 Schema | Name | Type  |  Owner   
--------+------+-------+----------
 public | tb   | table | postgres
(1 row)

postgres=# \c ziawang
You are now connected to database "ziawang" as user "postgres".
ziawang=# \d
        List of relations
 Schema | Name | Type  |  Owner   
--------+------+-------+----------
 public | tb   | table | postgres
(1 row)

ziawang=# drop table tb;
DROP TABLE
ziawang=# \d
No relations found.
[root@host ~]# su - postgres
-bash-4.1$ psql
psql (9.5.10)
Type "help" for help.

postgres=# \d
        List of relations
 Schema | Name | Type  |  Owner   
--------+------+-------+----------
 public | info | table | postgres
(1 row)

postgres=# drop table info;
DROP TABLE
postgres=# \l
                                  List of databases
    Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
------------+----------+----------+-------------+-------------+-----------------------
 postgres   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
            |          |          |             |             | postgres=CTc/postgres
 template1  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
            |          |          |             |             | postgres=CTc/postgres
 test       | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 ziawang    | ziawang  | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =Tc/ziawang          +
            |          |          |             |             | ziawang=CTc/ziawang
 ziawang_db | ziawang  | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =Tc/ziawang          +
            |          |          |             |             | ziawang=CTc/ziawang
(6 rows)





```


#### 同时删除多个表
- `drop table tb1, tb2, ...`

## 修改表信息
#### 增加字段
- `alter table 表名 add column 新字段名 字段属性`，新增的字段对于表中已经存在的行而言最初将先填充所给出的缺省值 （如果你没有声明DEFAULT子句，那么缺省是空值）。
- **注意**
	- 添加一个字段并填充缺省值将会导致更新表中的所有行(为了存储新字段的值)，但如果没有指定缺省值，PostgreSQL 就可以避免物理更新。所以如果我们将要在新字段中填充的值**大多数都不等于缺省值**，那么最好添加一个没有缺省值的字段，然后再使用 UPDATE 更新数据

#### 删除一个字段
- `alter table 表名 drop column 字段名`
- **当被删除字段被其他字段的外键引用的时候，postgresql不会删除该外键约束，如果我们想同时删除与该字段关联的外键约束**，使用下面命令
	- `alter table 表名 drop column 字段名 cascade`

#### 添加约束
###### check
- `alter table 表名 add [constraint name] check (expression)`

###### foreign key


###### unique 
- `alter table 表名 add [constraint name] unique (column1, column2...)`

###### not null 
- 需要使用 `set`  关键字
	- `alter table 表名 alter column 字段名 set not null ` 

###### default
- 需要使用`set`  关键字
	- `alter table 表名 alter column 字段名 set default`

#### 移除约束
###### 移除具有 constraint 别名的约束
- 移除具有别名的约束的方式和移除字段相同
	- `alter table 表名 drop column 别名`

###### 移除postgresql默认名称的约束
- 首先我们要获取这个约束的名称
	- `\d 表名`查看表详细信息
- `alter table 表名 drop column 表名`

###### 移除非空约束
- 非空约束由于没有自己的名称，因此删除非空约束的时候，我们不但要指定表还要制定这个表中的字段
	- `alter table 表名 alter column 字段名 drop not null`

###### 移除默认值约束
- 非空约束一样，也要制定字段
	- `alter table 表名 alter column 字段名 drop default`

###### 注意
- 删除外键约束时，就像删除字段一项，需要添加`cascade`
	- `alter table 表名 drop 字段名 cascade`

#### 修改约束
- 对于`null` 和`default`约束来说，修改约束和创建约束的方式相同


#### 修改字段名
- 需要使用rename关键字
	- `alter table 表名 rename column 字段名 to 新字段名`
#### 修改表名
- `alter table 表名 rename to 新表名`

## 查看表
#### 查看数据库下的所有表
- 使用`\d`命令


#### 查看表详细信息
- 使用`\d table_name`


