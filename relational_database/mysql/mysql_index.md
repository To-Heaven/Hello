# MySQL索引



## 磁盘IO与预读
- 磁盘IO	
	- 要从计算机磁盘中获取数据，至少要经过三个IO
		1. 平均寻道时间：磁头收到系统调用指令后，从开始移动到找到数据所在磁道所经历的平均时间一般在5ms以下
		2. 旋转延迟：即磁盘转速，磁盘转动一圈所需要的时间约为4ms
		3. 传输时间：从磁盘读写数据所需要的时间一般忽略不计
	-  综上，访问一次磁盘所需时间大约9ms左右，这个时间相对于CPU来说实在是太慢了。CPU在这段时间内可以完成几十万甚至上百万次指令

- 预读
	- 计算机操作系统所做的优化
	- **当读取数据遇到一次IO的时候，不光将当前磁盘地址指向的数据加载到缓冲区，而且还会将该地址相邻的地址也读取到内存的缓冲区中**

- 注意
	-  每一次经历IO读取数据的大小成为一页。（一页为4Kb或8Kb）
		-  降低了相同数据量所要经历磁盘IO的次数


## 索引
> cpu

- 对于一个包含庞大记录数据的表。使用where约束去查询其中的纪录时，如果不适用索引，那么MySQL就会遍历这张表的所有记录，没遍历一次表中记录都要经历一次IO，然后再返回满足where约束条件的记录。这样时非常浪费计算机CPU资源的
- 索引
	- 索引是一种数据结构，MySQL中的索引类型有`hash``BTree`两种。常用的`InnoDB和Myisam`引擎并不支持`hash`类型的索引结构，而且MySQL默认是使用`BTree`作为默认索引结构
- 索引的本质
	-  简单来说，索引其实就是一步一步的缩小数据的查询范围，直到最终筛选到目标数据。
		-  __索引把随机事件编程顺序事件__
			-  通过完全遍历的方式去查询目标数据是靠运气的，因为你不知道这个目标数据在什么位置
			-  索引是一种数据结构，将数据的分为进行分类，查询数据的时候，一步步缩小查询范围
		-  使用索引之所以能够大幅度提高效率是因为索引朝找数据的过程所经历的IO要比通过完全遍历查找数据经历的IO要少的多
- 索引能够使查询变得高效，但是对于数据的写入（插入、删除、修改）会降低效率（每次写入数据都会对即有索引结构产生影响）  

## 一个简单的BTree索引的数据结构
- 磁盘块（虚拟的概念）
	- 4Kb或8Kb
	- 从上往下呈指数增长
	- 操作系统与磁盘之间交流的最小单位
	- 注意：
		- 磁盘进行读写的最小单位是扇区，扇区是真实存在的物理区域

- 数据项
	- 数据的基本单位
	- BTree索引结构中，数据项存放着索引目标数据的磁盘物理位置和目标数据
		- 目标数据应该在BTree的最下层，这样的索引才最高效

- 指针
	- 记录着目标数据的物理地址


- 索引表
	- mysiam:
		- 会用一张表来单独存放索引
		- `.myi`用来单独存放索引
	- 数据是已经存放在内存和硬盘中的，因此创建b+tree是从下往上建立关系的
	- MySQL会先到索引表中找该数据对应的地址（存放在指针中），再根据地址去寻找该数据

>  每一个磁盘块能够存放的数据大小是一定的
> 第一层2^1 - 1
> 第n层 2^n-1
> 展示二进制位增长（层级越低，能够存放数据越多）

[一个简单的BTree索引的数据结构](https://github.com/ZiaWang/Hello/blob/master/picture/mysql_index.png?raw=true)
- 以`select * from tb where id=10`为例
	- 磁盘块1  --->  磁盘块2  --->  磁盘块6  --->  目标数据
- 数据查找
	1. 拿到查询条件`id=10`之后，会从索引结构的最上层（根）的磁盘块中往下查找
	2. 通过二分查找获取对应指针
	3. 指针中包含着另一磁盘块的地址
	4. 在该磁盘块中继续二分查找，再获取对应指针
	5. 该指针指向最终结果所在的磁盘块中（注意，磁盘块是一个虚拟概念，数据是存在放在磁盘块空间的数据项中）或者下一个数据范围


## 创建表索引
- 在创建表的时候创建索引
	- 创建主键`primary key`即是创建了主键索引
	- 创建唯一键`unique key`即是创建了唯一索引
	- 在创建表时创建普通索引，只能在表的字段完成定义后使用`index` 创建

```sql
    　　CREATE TABLE 表名 (
                字段名1  数据类型 [完整性约束条件…],
                字段名2  数据类型 [完整性约束条件…],
                [UNIQUE | FULLTEXT | SPATIAL ]   INDEX | KEY
                [索引名]  (字段名[(长度)]  [ASC |DESC]) 
                );

-------------------------------------------
CREATE TABLE t(
id INT AUTO_INCREMENT,
obj_name VARCHAR(15),
INDEX(id, obj_name)
);

```

- 在创建表之后单独创建索引
	- 创建主键索引
		- `alter table tb add primary key(field1[, field2. . .])`
	- 创建唯一索引
		- `alter table tb add unique key(field1[, field2. . . ] )`
	- 创建普通索引
		- `create index index_name on table(field1[,field2. . . ])`	
		- `alter table table_name add index index_name(field1, field2..)`  

```sql
CREATE [UNIQUE|FULLTEXT|SPATIAL] INDEX index_name
    [index_type]
    ON tbl_name (col_name [(length)] [ASC | DESC],...)
    [index_option]
    [algorithm_option | lock_option] ...

```


- 删除索引
	- 删除主键索引
		- `alter table table_name  drop primary key`
	- 删除唯一索引和普通索引
		- `drop index index_name on table_name`



## MySQL建立索引要遵守的规则
- 索引字段要尽量的小
	- IO的次数与BTree的高度（层数）有关，层数越高，IO次数越多。
	- 索引字段越小，数据项就越小，那么每一个磁盘块的数据项的数量就越多（数据项数量=磁盘块大小/数据项大小）。当数据量不变的情况下，BTree的高度与数据项的数据呈反向变化关系，数据项的数量越多，BTree的层数就越少，IO次数就越少。
	- 综上，索引字段越小，数据项越小，数据项的数目就越大，从而BTree树层数就越少
- 索引的最左匹配特性
	- 当索引是由多个键组成的时候，比如index(name, age, gender)，Btree是按照从左到右的顺序建立搜索的。

## 字段值的区分度
- 字段区分度简单来说，就是一张表中字段对应值的不重复的比例
	- 公式`count(distinct field)/count(*)`

- 区分度在查询中的应用
	- 一个字段的区分度越高，那么该字段对应的BTree树的层数就越少，查询起来就越快
	- 一个字段对应的全部值都相同，那么BTree的磁盘块中存放的数据项只有这一个重复的值，并且BTree的层数就等于这个字段重复值的次数，这样查询效率显然是非常低的

## 索引查询测试
> 这里使用存储过程创建了一个有66869个记录的表，详细信息如下


```sql
mysql> select count(*) from s;
+----------+
| count(*) |
+----------+
|    66869 |
+----------+
1 row in set (0.06 sec)

mysql> desc s;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | YES  |     | NULL    |       |
| NAME   | varchar(20) | YES  |     | NULL    |       |
| gender | char(6)     | YES  |     | NULL    |       |
| email  | varchar(50) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql> select * from s limit 10;
+------+---------+--------+-------------------+
| id   | NAME    | gender | email             |
+------+---------+--------+-------------------+
|    1 | ziawang | male   | ziawang1@xxx.com  |
|    2 | ziawang | male   | ziawang2@xxx.com  |
|    3 | ziawang | male   | ziawang3@xxx.com  |
|    4 | ziawang | male   | ziawang4@xxx.com  |
|    5 | ziawang | male   | ziawang5@xxx.com  |
|    6 | ziawang | male   | ziawang6@xxx.com  |
|    7 | ziawang | male   | ziawang7@xxx.com  |
|    8 | ziawang | male   | ziawang8@xxx.com  |
|    9 | ziawang | male   | ziawang9@xxx.com  |
|   10 | ziawang | male   | ziawang10@xxx.com |
+------+---------+--------+-------------------+
10 rows in set (0.00 sec)

mysql>

```

> id字段和Email区分度为最高，而对应name和gender字段则区分度最低，全部是重复相同的值      
> 开始测试

- 此时，id没设置主键，表中不存在索引

```sql
mysql> select count(*) from s;
+----------+
| count(*) |
+----------+
|    66869 |
+----------+
1 row in set (0.05 sec)

mysql> select count(*) from s where id=66666;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.05 sec)

mysql> select count(*) from s where id=1;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.05 sec)

mysql> select count(*) from s where id>66666;
+----------+
| count(*) |
+----------+
|      203 |
+----------+
1 row in set (0.05 sec)
mysql> select count(*) from s where id between 55555 and 55557;
+----------+
| count(*) |
+----------+
|        3 |
+----------+
1 row in set (0.07 sec)

mysql>
```

- 结论： 没有索引情况下
	1.  不管查询什么未知的值都会便利整张列表，对表中任何位置的记录的查询所花费的时间和遍历整个表所需要的时间基本相同
	2.  对于模糊查询，`<  >  like  between` ，也是会遍历整张表，同样耗时

 
- 现在为id字段设置索引

```sql
mysql> create index a on s(id);
Query OK, 0 rows affected (2.27 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>
```

> 开始测试

```sql
mysql> select count(*) from s where id=1;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s where id=66666;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s where id>1;
+----------+
| count(*) |
+----------+
|    66868 |
+----------+
1 row in set (0.06 sec)

mysql> select count(*) from s where id>66666;
+----------+
| count(*) |
+----------+
|      203 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s where id<66666;
+----------+
| count(*) |
+----------+
|    66665 |
+----------+
1 row in set (0.06 sec)

mysql> select count(*) from s where id<6;
+----------+
| count(*) |
+----------+
|        5 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s where id between 1 and 66666;
+----------+
| count(*) |
+----------+
|    66666 |
+----------+
1 row in set (0.06 sec)

mysql> select count(*) from s where id between 55555 and 55557;
+----------+
| count(*) |
+----------+
|        3 |
+----------+
1 row in set (0.00 sec)
mysql> select count(*) from s where id like 555;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.06 sec)

mysql> select count(*) from s where id like 55555;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.07 sec)

mysql>
```

- 结论：索引字段为单个字段时
	1. 如果是针对确定值的查询，速度会非常快
	2. 如果是对模糊值的查询，要看模糊查询的种类
		1. 如果是在一个可变的范围内查询，范围越大，查询时间越久
		2. 如果是使用like查询，本质上就相当于在整张表中查询，范围最大，所以时间最久

- 现在测试约束条件中包含其他非索引字段的情况

```sql
mysql> desc s;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | YES  | MUL | NULL    |       |
| NAME   | varchar(20) | YES  |     | NULL    |       |
| gender | char(6)     | YES  |     | NULL    |       |
| email  | varchar(50) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql> select  count(*) from s where id=34567 and name='ziawang';
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

mysql> select  count(*) from s where id=34567 and name like '%wang';
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

mysql> select  count(*) from s where id=34567 and email='xxxxxxxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> select  count(*) from s where id>1 and email='xxxxxxxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.07 sec)

mysql> select  count(*) from s where id>55555 and email='xxxxxxxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.06 sec)

mysql> select  count(*) from s where id>66666 and email='xxxxxxxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.06 sec)

mysql> select  count(*) from s where id<2 and email='xxxxxxxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> select  count(*) from s where id between 55555 and 55557 and email='xxxxxxxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> select  count(*) from s where id between 5 and 55557 and email='xxxxxxxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.07 sec)

mysql> select  count(*) from s where id>66666 and email='xxxxxxxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

```


- 结论：只要查询的字段中有区分度较高的字段作为索引，那么上述单个索引的查询规律不变
	1. 如果是针对确定值的查询，速度会非常快
	2. 如果是对模糊值的查询，要看模糊查询的种类
		1. 如果是在一个可变的范围内查询，范围越大，查询时间越久
		2. 如果是使用like查询，本质上就相当于在整张表中查询，范围最大，所以时间最久



- 再看覆盖索引
	- 所谓覆盖索引，即在索引文件中直接获取数据。
	- 现在添加一个区分度为最低的name字段为索引

```sql
mysql> create index b on s(name);
Query OK, 0 rows affected (2.41 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc
    -> s;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | YES  | MUL | NULL    |       |
| NAME   | varchar(20) | YES  | MUL | NULL    |       |
| gender | char(6)     | YES  |     | NULL    |       |
| email  | varchar(50) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql>
```

>  现在有了两个 索引，其中id为区分度高的索引，name为去分组最低的索引

```sql
mysql> select count(*) from s where name='ziawang' and email='xxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.16 sec)

mysql> select count(*) from s where name='xxxxx' and email='ziawang_python@163.com';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s where name='ziawang' and id=1
    -> ;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s where name='ziawang' and id=66666;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s where name='ziawang' and id>66666;
+----------+
| count(*) |
+----------+
|      203 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s where name='ziawang' and id>1;
+----------+
| count(*) |
+----------+
|    66868 |
+----------+
1 row in set (0.17 sec)

mysql> select count(*) from s where name='ziawang' and id>55555;
+----------+
| count(*) |
+----------+
|    11314 |
+----------+
1 row in set (0.17 sec)

mysql> select count(*) from s where name='ziawang' and id>55555;
+----------+
| count(*) |
+----------+
|    11314 |
+----------+
1 row in set (0.16 sec)

mysql>

```


- 结论：
	- 约束条件中只有一个索引字段，且区分度较低的时候（重复度高）
		- 如果等号右边判断条件不属于该索引字段范围，查询会比较快，因为在BTree上不存在满足该值的数据项
		- 如果该值在索引字段范围内(模糊查询)，搜索会很慢
	- 约束条件中还有其他索引器端，并且区分度较高
		- 搜索速度就会取决于该区分度高的索引字段
		- 此时规律就和上面单个索引字段的情况相同
	

- 索引合并
	- 索引合并其实就是一个索引创建时同时指定了多个字段  `index(name, age . . . . )`
	-  索引和并遵循最做匹配原则

> 现在删除已经创建的索引，建立一个联合索引


- 先将区分度最低的两字段放在最左边

```sql
mysql> drop index a on s;
Query OK, 0 rows affected (0.18 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> drop index b on s;
Query OK, 0 rows affected (0.15 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> create index a_b_c_d on s(name, gender, id, email);
Query OK, 0 rows affected (3.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>

```


- 看代码

```sql
mysql> select count(*) from s
    -> where name='xxxxxxx'  and gender='male' and id=1 and email='ziawang1@xxx.com';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s
    -> where name='ziawang'  and gender='xxxxxx' and id=1 and email='ziawang1@xxx.com';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from s
    -> where name='ziawang'  and gender='male' and id>1 and email='ziawang1@xxx.com';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.17 sec)

mysql> select count(*) from s
    -> where name='ziawang'  and gender='male' and id=1 and email='xxxxx';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql>
```

- 结论， 如果将区分度较小的字段放在联合索引左边
	- 会按照最左边的索引字段的搜索规律搜索
		- 如果搜索的条件值不在该字段值内，搜索会很快
		- 如果搜索的条件在在该字段值范围内，搜索会比较慢，因为他的区分度低
	- 会按照bool值逻辑运算的顺序进行判断
		- 如果第一个就False，也就是上面说的条件值不再字段值内，就很快直接得出结果0rows
		- 如果第一个为True，就会享有看第二个，一次类推，遇到索引字段的区分度决定搜索的快慢
		

## 测试后结论
1. 建立联合索引时，要将区分度高的字段放在最左边（最左前缀匹配）
2. 补充：
	1.  索引字段如果参与计算也会降低查询速度，所以不要将参与计算或者当作函数参数的字段设置为索引字段
	2.  对于约束条件为`or`的字段，应将`or`两边的字段分别单独设置为索引字段




## 索引优化
- 避免使用`select * `
- 使用`count(1)`或`count(field)` 代替`count(*)`
- 创建表时，尽量用char代替varchar
- 多条件查询的时候，使用组合索引代替单列索引
- 尽量使用短索引，即索引字段要尽量小
- 使用`join` 代替子查询(sub-squery)
- 连表时，条件类型需要一致
- 字段的散列值（区分度）较小的时候，不适合使用索引


## 存储过程——用于测试





## 关键字 explain
