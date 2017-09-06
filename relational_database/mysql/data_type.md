# 数据类型
- 分不同的种类存不同的数据

# 字段数据类型
- 格式


```sql
create table 表名(
字段名1  数据类型1  [(宽度)  约束条件],
字段名2  数据类型2  [(宽度)  约束条件],
字段名3  数据类型3  [(宽度)  约束条件]
）
```

## 宽度
- 对于数值来说
	- 宽度仅仅限制传入的值在表中显示宽度
		- 当数值实际宽度大于宽度时，该参数没有意义
		- 当数值实际宽度小于指定宽度时，使用zerofill约束条件可以填充不足
	- 宽度只与显示有关，与存储无关
		- 显示宽度只用于显示，并不能限制取值范围和占用空间
- 对于其他类型（BIT,CHAR,VARCHAR等）
	- 宽度限制了其传入值的长度
		- 对于BIT
			- 设定的宽度值代表了传入数据的所使用的内存中最大的位数
		- 对于CHAR, VARCHAR
			- 由于每一个char,varchar子都占用的是一个byte，因此设定的宽度值代表的字符的长度  


## 数值
> mysql 中常用的数值类型有tinyint, int, bigint 三种

- tinyint
	- 范围
		- 没有`unsigned`约束  ---> `-128~127`
		- 有`unsigned`约束  ---> `0~255`
	- 一个字节（8个bit位）
		-  最开头的那1 个bit位用1/0表示正负，因此最大127

- int
	- 范围
		- 没有`unsigned`约束  ---> `-2147483648~2147483467` (10)
		- 有``unsigned`约束  ---> `0~4294967295`
	-四个字节（32个bit位）
		- 最大表示10位数，加上钱买你正负号，所以默认宽度11
	
	 
- bigint
	- 范围
		- 没有`unsigned`约束  ---> `-2^63-1~2^63` (10)
		- 有``unsigned`约束  ---> `0~2^64-1`
	-四个字节（32个bit位）
		- 最大表示10位数，加上钱买你正负号，所以默认宽度11


- float
	- 单精度
	- 数值越大越不准确
	- 4字节


- double
	- 双精度
	- 数值越大越不准确
	- 8字节


- decimal
	- 精确
	- `DECIMAL(M, D)`
		- 范围依赖M, D 的值


- BIT
	- BIT(M)
		- M范围是`1-64`， 不指定则默认为储存的记录
	- 直接查看是无法显示的，需要用到函数转换操能查看值
		- 有时直接取会冒出很奇怪的字符 

```sql
mysql> desc t;
+-------+--------+------+-----+---------+-------+
| Field | Type   | Null | Key | Default | Extra |
+-------+--------+------+-----+---------+-------+
| id    | bit(1) | YES  |     | NULL    |       |
+-------+--------+------+-----+---------+-------+
1 row in set (0.00 sec)

mysql> insert into t values
    -> (2);
ERROR 1406 (22001): Data too long for column 'id' at row 1

mysql> alter table t modify id bit(2);
Query OK, 0 rows affected (0.73 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> insert into t values
    -> (2);
Query OK, 1 row affected (0.05 sec)

mysql> insert into t values
    -> (3);
Query OK, 1 row affected (0.05 sec)

mysql> insert into t values
    -> (4);
ERROR 1406 (22001): Data too long for column 'id' at row 1
mysql> select * from t;
+------+
| id   |
+------+
|     |
|     |
+------+
2 rows in set (0.00 sec)

mysql> select bin(id) from t;
+---------+
| bin(id) |
+---------+
| 10      |
| 11      |
+---------+
2 rows in set (0.06 sec)

mysql> select hex(id) from t;
+---------+
| hex(id) |
+---------+
| 2       |
| 3       |
+---------+
2 rows in set (0.09 sec)

mysql> select bin(id),hex(id) from t;
+---------+---------+
| bin(id) | hex(id) |
+---------+---------+
| 10      | 2       |
| 11      | 3       |
+---------+---------+
2 rows in set (0.00 sec)

mysql>
```




## 字符

- char
	- 宽度：
		- char(宽度) 宽度指的是字符(可以是中文或其他)长度，与存储有关（只有数字与存储无关）
	- 定长：存入字符长度小于宽度，用空格补齐凑够固定长度存放到表中记录起来
	- 优缺点：
		- 存取速度快，不需要计算
		- 浪费空间资源
	- 需要用引号包含起来
		- 引号中的空格也算一个字符 
		- __sql的where语句会自动忽略掉字符尾部的空格__
	
- varchar
	- 变长：存入字符长度小于宽度，不需要附加空格处理
	- 优缺点：
		- 存取速度慢，要先计算长度
			-  存，先计算字符长度，将字符长度放在字符头，以便下次取
				-  这个头会占用一个字符
			-  取，先获取头中字符长度信息，再取数据
		- 节省空间
	- 引号中的空格也算一个字符
		- mysql 在使用where 比较的时候，__会引号中去掉末尾的空格，但是不会去掉开头的空格__
		- `select  id, name where name = 'ziawang            '`
			- char  varchar中字符最后如果有空格，在比较时候也会被忽略

- 对于char和varchar的比较
	- 都是只比较他们的值，char会忽略后面添加的空格。
	- 即使使用下面`set sql_mode = 'pad_char_to_full_length'`也一样只会表值

- 查看字符串的长度
	- 在char字符的长度小于宽度的时候，MySQL会在字符尾部填充空格至长度与宽度相同。而varchar则不会被填充空格
	- 当我们使用函数`select char_length(字段名) from 表名`的时候，char填充的空格不会被算入长度
	- 在当前会话中先使用命令`set sql_mode = 'pad_char_to_full_length'`，再使用`select char_length(字段名) from 表名`就可以获取char字符经过填充字符串之后的长度
	- Note
		- `set sql_mode = 'pad_char_to_full_lebgth'`命令在重启服务端之后失效

- length  
	- 查看数据所占字节长度
	- `select length() from t1`


```sql

mysql> create table t2(field1 char(5), field2 varchar(5));
Query OK, 0 rows affected (0.27 sec)

mysql> insert into t2 values
    -> ('a', 'a');
Query OK, 1 row affected (0.05 sec)

mysql> select * from t2;
+--------+--------+
| field1 | field2 |
+--------+--------+
| a      | a      |
+--------+--------+
1 row in set (0.00 sec)

mysql> select char_length(field1), char_length(field2) from t2;
+---------------------+---------------------+
| char_length(field1) | char_length(field2) |
+---------------------+---------------------+
|                   1 |                   1 |
+---------------------+---------------------+
1 row in set (0.06 sec)

mysql> set sql_mode = 'pad_char_to_full_length';
Query OK, 0 rows affected, 1 warning (0.02 sec)

mysql> select char_length(field1), char_length(field2) from t2;
+---------------------+---------------------+
| char_length(field1) | char_length(field2) |
+---------------------+---------------------+
|                   5 |                   1 |
+---------------------+---------------------+
1 row in set (0.00 sec)

mysql>

```




> 对于比较大的资源，一般将这些资源存放在另一台主机上，这个主机要有足够大的硬盘和内存，用于快速的数据存取，当客户端向服务端发起该资源的请求的时候，服务端返回该资源所在的位置，然后客户端根据这个位置去获取资源，很大程度上降低了MySQL服务器的压力



## 日期
- datetime
	- `2017-09-06 10:30:10`
	- `20170906103000`
	- 注册时间
	- 用引号传递参数

- date
	- `2017-09-06`
	- `20170906`
	- `2017/09/06`
	- 出生日期
	- 用引号传递参数
	- 范围
		- `1000-01-01 ~ 9999-12-31`

- time
	- `10:30:10`
	- `103000`
	- 用引号传递
	- 范围
		- `-838:59:59 ~ 838:59:59`

- year 
	-  `2017`
	-  可以是数字，也可以是数字
	-  无论tear设置何种宽度，最终都是year(4)
	-  范围
		-  1901 ~ 2155

- timestamp
	- `20170906 150005`
	- 范围
		- 1970-01-01 00:00:00 ~ 2037 年某时


- 函数
	- now()
	- 当前时间

```sql
mysql> create table t4(
    -> a datetime,
    -> b date,
    -> c year,
    -> d time,
    -> e timestamp
    -> );
Query OK, 0 rows affected (0.30 sec)

mysql> insert into t4 values(now(),now(),now(),now(),now());
Query OK, 1 row affected, 1 warning (0.05 sec)

mysql> select * from t4;
+---------------------+------------+------+----------+---------------------+
| a                   | b          | c    | d        | e                   |
+---------------------+------------+------+----------+---------------------+
| 2017-09-06 14:40:31 | 2017-09-06 | 2017 | 14:40:31 | 2017-09-06 14:40:31 |
+---------------------+------------+------+----------+---------------------+
1 row in set (0.00 sec)

mysql>

```






## 枚举与集合
- enum枚举
	- 规定一个范围，可以有多个值，但是为该字段传值的时候，只能取规定范围中的一个
- set集合（与python不同）
	-  规定个一个范围，可以有多个值，为该字段传值的时候，可以取堆顶范围内的多个
-注意:
	- 如果存放的数据不在enum或set范围之内，存放不会报错，但是会警告，而且传入的值为空字符（不是null）
	- 如果存放的数不在enum或set范围之内且为null，那么null就会存放在记录中，可以为枚举和集合设置not null约束
	- 这种情况下如果在枚举位置不传入值，就会默认枚举中第一个值存放到记录
	- 对于非枚举和集合，默认会传入null到记录


```sql


```














































- int（default=11）
	- 默认宽度11
