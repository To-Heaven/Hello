# 数据操作

## 插入数据 INSERT
- 插入一条完整数据
	- `INSERT INTO 表名(字段1,字段2,字段3…字段n) VALUES(值1,值2,值3…值n);`
	- `INSERT INTO 表名 VALUES (值1,值2,值3…值n);`
		- 当不指定字段的时候，默认所有字段

```sql
mysql> insert into t values
    -> ('a', 10),
    -> ('b', 11);
ERROR 1136 (21S01): Column count doesn't match value count at row 1
mysql> insert into t(name, age) values
    -> ('a', 10),
    -> ('b', 11);
Query OK, 2 rows affected (0.16 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> select * from t;
+----+------+------+
| id | name | age  |
+----+------+------+
|  1 | a    |   10 |
|  2 | b    |   11 |
+----+------+------+
2 rows in set (0.00 sec)
```

- 在指定字段插入一条数据
	- `INSERT INTO 表名(字段1,字段2,字段3…) VALUES (值1,值2,值3…);`
		- 只指定部分字段，在values后面要把对应指定字段的值全部写入

```sql
mysql> insert into t(name) values
    -> ('zia');
Query OK, 1 row affected (0.04 sec)

mysql> select * from t;
+----+------+------+
| id | name | age  |
+----+------+------+
|  1 | a    |   10 |
|  2 | b    |   11 |
|  3 | zia  | NULL |
+----+------+------+
3 rows in set (0.00 sec)

mysql>
```

- 插入多条记录
	
```
# 格式

insert into 表名(字段1, 字段2,字段3...) values
		(值1,值2,值3),
		(值1,值2,值3),
		(值1,值2,值3);

```


```sql
mysql> insert into t(age, phone) values
    -> (1, '148948489489'),
    -> (2, '148484884484');
Query OK, 2 rows affected (0.05 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> select * from t;
+----+------+------+--------------+
| id | name | age  | phone        |
+----+------+------+--------------+
|  1 | a    |   10 | NULL         |
|  2 | b    |   11 | NULL         |
|  3 | zia  | NULL | NULL         |
|  4 | NULL |    1 | 148948489489 |
|  5 | NULL |    2 | 148484884484 |
+----+------+------+--------------+
5 rows in set (0.00 sec)

mysql>
```

- 将查询的结果插入指定记录中

```
# 格式
insert into 表名(字段1, 字段2,字段3...)  
		select (字段1, 字段2,字段3...)  from 表2
		where 条件;
```


```sql 


```




## 查询数据 SELECT
- 基本查询方法

```
# 基本格式
select (字段1,字段2,字段3) from 表名
		where  条件;

```



## 修改数据 UPDATE
- 基本修改语法

```
# 格式
update 表名 set 
		字段1=值1,
		字段2=值2,
		where 条件;
```



```sql
mysql> update t set
    -> name='c'
    -> where age=1;
Query OK, 1 row affected (0.36 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from t;
+----+------+------+--------------+
| id | name | age  | phone        |
+----+------+------+--------------+
|  1 | a    |   10 | NULL         |
|  2 | b    |   11 | NULL         |
|  3 | zia  | NULL | NULL         |
|  4 | c    |    1 | 148948489489 |
|  5 | NULL |    2 | 148484884484 |
+----+------+------+--------------+
5 rows in set (0.00 sec)

mysql>

```




## 删除数据 DELETE
- delete删除整条记录
	- 如果delete删除所有记录之后，如果存在id自增主键，再次插入记录，就会从之前id结束的地方继续算起


```
# 格式
delete from 表名
	where 条件;

```

```sql
mysql> delete from t
    -> where id =5;
Query OK, 1 row affected (0.08 sec)

mysql> select * from t;
+----+------+------+--------------+
| id | name | age  | phone        |
+----+------+------+--------------+
|  1 | a    |   10 | NULL         |
|  2 | b    |   11 | NULL         |
|  3 | zia  | NULL | NULL         |
|  4 | c    |    1 | 148948489489 |
+----+------+------+--------------+
4 rows in set (0.00 sec)

mysql>
```



- truncate清空所有记录
	- truncate清空列表，如果存在id自增主键，再次插入记录时从0开始算起


```
# 格式
truncae table 表名

```



```sql
mysql> select * from t;
+----+------+------+--------------+
| id | name | age  | phone        |
+----+------+------+--------------+
|  1 | a    |   10 | NULL         |
|  2 | b    |   11 | NULL         |
|  3 | zia  | NULL | NULL         |
|  4 | c    |    1 | 148948489489 |
+----+------+------+--------------+
4 rows in set (0.00 sec)

mysql> truncate table t;
Query OK, 0 rows affected (0.26 sec)

mysql> select * from t;
Empty set (0.00 sec)

mysql>

```
