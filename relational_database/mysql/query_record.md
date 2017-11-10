# 单表查询
## 单表查询




- 参考操作表

```sql
+--------------+-----------------------+------+-----+---------+----------------+
| Field        | Type                  | Null | Key | Default | Extra          |
+--------------+-----------------------+------+-----+---------+----------------+
| id           | int(11)               | NO   | PRI | NULL    | auto_increment |
| name         | varchar(20)           | NO   |     | NULL    |                |
| sex          | enum('male','female') | NO   |     | male    |                |
| age          | int(3) unsigned       | NO   |     | 28      |                |
| hire_date    | date                  | NO   |     | NULL    |                |
| post         | varchar(50)           | YES  |     | NULL    |                |
| post_comment | varchar(100)          | YES  |     | NULL    |                |
| salary       | double(15,2)          | YES  |     | NULL    |                |
| office       | int(11)               | YES  |     | NULL    |                |
| depart_id    | int(11)               | YES  |     | NULL    |                |
+--------------+-----------------------+------+-----+---------+----------------+

create table employee(
id int primary key auto_increment,
name varchar(20) not null,
sex enum('male', 'female') default 'male' not null,
age int(3)  unsigned not null default 28 ,      # not null 要写在约束条件之后
hire_date date not null ,
post varchar(50),
post_comment varchar(100) ,
salary double(15, 2),
office int ,
depart_id int
);

insert into employee(name,sex,age,hire_date,post,salary,office,depart_id) values
('a','male',18,'20170301','doctor',7300.33,401,1),
('b','male',78,'20150302','teacher',1000000.31,401,1),
('c','male',81,'20130305','teacher',8300,401,1),
('d','male',73,'20140701','teacher',3500,401,1),
('e','male',28,'20121101','teacher',2100,401,1),
('f','female',18,'20110211','teacher',9000,401,1),
('g','male',18,'19000301','teacher',30000,401,1),
('h','male',48,'20101111','teacher',10000,401,1),

('i','female',48,'20150311','sale',3000.13,402,2),
('j','female',38,'20101101','sale',2000.35,402,2),
('k','female',18,'20110312','sale',1000.37,402,2),
('l','female',18,'20160513','sale',3000.29,402,2),
('m','female',28,'20170127','sale',4000.33,402,2),

('n','male',28,'20160311','operation',10000.13,403,3), 
('chengyaojin','male',18,'19970312','operation',20000,403,3),
('o','female',18,'20130311','operation',19000,403,3),
('p','male',18,'20150411','operation',18000,403,3),
('q','female',18,'20140512','operation',17000,403,3)
;
```

- 查询语法

```sql
SELECT 字段1,字段2... FROM 表名
                  WHERE 条件
                  GROUP BY field
                  HAVING 筛选
                  ORDER BY field
                  LIMIT 限制条数

```




## 关键字的执行优先级别

```
# 从上往下,优先级从大到小
from 
where
group by
聚合函数
having
select
order by 
limit
```

- 执行顺序
	1. from首先找到表
	2. 根据where约束的条件**从文件中取出对应记录结果**
	3. 将取出的数据进行分组（如果没有分组，where得到的记录结果整体就当做一组）
	4. 创建虚拟表，**虚拟表字段来源于group by 指定的字段和SQL语句中的聚合函数**
	5. 使用having关键字对虚拟表中存在的字段进行过滤
	6. 得到select结果
	7. order by 对select结果进行排序
	8. limit 指定显示select结果的记录条数

> 注意，having执行过滤条件是基于group by 之后使用指定字段创建的新表


- `select sex, group_concat(name) from tb where name like 'zia___g' group by  sex having count(id)>5` 
	- 执行顺序应该是这样
		1. 执行from子句，找到目标表tb
		2. 执行where子句，获取tb中满足where后condition的记录，加载到内存
		3. 执行group by 子句，将获取的记录进行分组，在内存中创建虚拟表，字段为sex，group_concat(name)，count(id)
		4. 执行having，对虚拟表中count(id)字段进行过滤，保留虚拟表中满足count(id)>5的记录
		4. 执行select打印查询结果

- **注意：where和having是完全不同的**
	1. where是约束声明，having是过滤声明
	2. MySQL会拿着where指定的条件去文件中取数据，而having则实在取出数据之后进行过滤
		1. 执行where约束是对硬盘中存有记录的的文件的操作，在select结果之前就执行
		2. 执行having语句是对内存中group by 分组之后得到的表的过滤操作
	3. 执行优先级：`where > group by > 聚合函数 > having`
		1. having放到group by 之后
		2. where只能放在group之前
		3. 查询过程中，聚合语句(sum,min,max,avg,count)要比having子句优先执行。而where子句在查询过程中执行优先级高于聚合语句。


```sql
select count(id) from employee where salary > 10000; #正确，分析：where先执行，后执行聚合count(id),然后select出结果
select count(id) from employee having salary > 10000;#错误，分析：先执行聚合count(id)，后执行having过滤，在聚合之后得到的表中没有salary字段，只有count(id)字段。无法对id进行salary>10000的过滤
select post,group_concat(name) from employee group by post having salary > 10000;#错误，分组后无法直接取到salary字段
select post,group_concat(name) from employee group by post having avg(salary) > 10000;  #  正确
```



## where
- 是约束条件而不是过滤条件，因为此时数据还未产生出来
	- 过滤条件：记录出来之后，再用having进行过滤
- `>   <  =  `单条件查询
- `and or `多条件查询
	- `and`优先级高于`or`
	- **使用括号提高可读性**



- ` x  between a and b`  <==>  ` x >= a and x <= b`
	- `x not between a and b `

- `is null`
	- 判断某个字段是否为null不能用 `=`，应该用`is`
	- null代表什么都没有（什么数据类型数据都没有），不等于空字符串

- `in (a, b, c, d)`
	- 匹配值在括号范围内的数据  


- `like`
	- `like '%happy%'`
		- 通配符`%` 表示任意长度的任意字符
		- 匹配包含`happy`的字符串
	- `like 'ha__y'`
		- 通配符`_`表示单个任意字符
		- 匹配以ha好头，y结尾，中间有两个任意长度字符的字符
- `regexp`
	- 正则表达式匹配
	- 格式`field_name REGEXP expression `

```sql
select * from employee where age>30 and age%2=1;
select * from employee where post='teacher' and name in ('a', 'c', 'd');
select * from employee where post regexp 'er$';
```


## 模式匹配
- 使用`like`和`not like`
	- 使用`_`匹配任意单个字符
	- 使用`%`匹配任意数量的字符（包括0个）

```sql
mysql> SELECT * FROM pet WHERE name LIKE 'b%';
mysql> SELECT * FROM pet WHERE name LIKE '%fy';
mysql> SELECT * FROM pet WHERE name LIKE '%w%';
mysql> SELECT * FROM pet WHERE name LIKE '_____';
```

- 使用`regexp` 和`not regexp`（等价于`rlike`和`not rlike`）
	- `.` 匹配任意单个字符
	- `[]`字符组，与括号内的任意单个字符匹配
	- `*`匹配其前面0个或多个实例
		- `.*`匹配任何数量的任何东西
	-  `^`匹配以^之后字符开头的实例
	-  `$`匹配以$之前字符结尾的实例
	-  {n, m}
		-  重复n到m次
	-  {n}
		-  重复n次
```sql
mysql> SELECT * FROM pet WHERE name REGEXP '^.....$';
mysql> SELECT * FROM pet WHERE name REGEXP '^.{5}$';
mysql> SELECT * FROM pet WHERE name REGEXP 'w';
mysql> SELECT * FROM pet WHERE name REGEXP 'fy$';


```


## group by 
- **可以按照指定的任意字段分组，但是分组完成之后只能查看分组的那个字段，要想获取组内的其他字段信息需要借助函数**
- **如果查询语句中不使用group by，那么整张表会按照内存中通过where得到的表的每个字段进行分组**
- 函数
	- group_concat(field)
		- `select sex from tb group by sex`
			- 如果使用sex字段来进行分组，那么只能查看sex字段分组的结果，要想查看该分组结果中的其他字段的值，必须使用`group_concat(name)`函数，将数据聚合成一条数据才能显示，如果不使用此函数，那么满足sex组(group)的name数据旧远多于一条记录，无法在一个空格中显示出来，因此需要聚合起来
		- 将查询到记录的field字段的值聚合成一串字符，这样就可以作为单个数据中显示
	- count(field)    统计分组中字段field的数量
	- max(field)    返回分组中字段field的最大值
	- min(field)    返回分组中字段field的最小值
	- avg (field)    返回分组中字段field的平均值
	- sum(field)    返回分组中字段的和


- 给分组的字段取别名
	1. 使用`as`
	2. 不使用`as`

```sql
mysql> select  group_concat(name) '姓名', post as '职位'  from employee
    -> where age > 30
    -> group by post
    -> having count(id)<3;
+--------+--------+
| 姓名   | 职位   |
+--------+--------+
| i,j    | sale   |
+--------+--------+
1 row in set (0.10 sec)

mysql>
```



## having  过滤
> where：利用约束条件将记录从硬盘文件读到内存 	
> having : 对内存中group by 分组之后的结果进行过滤

- 对内存中group by 分组之后得到的表的过滤操作
- 注意：
	- 如果`having`子句中的筛选条件存在聚合函数，那么前面必须要有`group  by` 关键字的子句
	- `having`作用的对象是分组之后的结果
	- `group by ` 分组之后才能使用函数聚合，即聚合优先级比`group `小 ,比`having`大
	- 聚合函数可以写在having中，这种情况，在执行having之前先使用聚合函数进行聚合，having再基于这个结果进行过滤：
		- `select max(salary) from employee where id>2 group by depart_id having count(id) > 2`
			- 上述语句，会在内存中分组得到的表中产生三个字段   depart_id, max(salary), count(id)





```sql
mysql> select * from employee where id>17;
+----+------+--------+-----+------------+-----------+--------------+----------+--------+-----------+
| id | name | sex    | age | hire_date  | post      | post_comment | salary   | office | depart_id |
+----+------+--------+-----+------------+-----------+--------------+----------+--------+-----------+
| 18 | q    | female |  18 | 2014-05-12 | operation | NULL         | 17000.00 |    403 |         3 |
+----+------+--------+-----+------------+-----------+--------------+----------+--------+-----------+
1 row in set (0.08 sec)

mysql> select * from employee having id > 17;
+----+------+--------+-----+------------+-----------+--------------+----------+--------+-----------+
| id | name | sex    | age | hire_date  | post      | post_comment | salary   | office | depart_id |
+----+------+--------+-----+------------+-----------+--------------+----------+--------+-----------+
| 18 | q    | female |  18 | 2014-05-12 | operation | NULL         | 17000.00 |    403 |         3 |
+----+------+--------+-----+------------+-----------+--------------+----------+--------+-----------+
1 row in set (0.06 sec)

mysql> select count(id) from employee where id > 17;
+-----------+
| count(id) |
+-----------+
|         1 |
+-----------+
1 row in set (0.00 sec)

mysql> select count(id) from employee having id > 17;
ERROR 1054 (42S22): Unknown column 'id' in 'having clause'
mysql>

```



## order  by
- 对结果按照指定的字段进行排序，默认为升序`asc`排序
- 升序（即从上往下  <-->  从大到小）
 	- `order by  field asc` 
	- `order by  field`
- 降序
	- `order by field desc`
- 利用多个字段进行排序
	- `order by field, field desc`
		- 先按照field1升序排，对于field值相同的记录再用field2排列   


## limit
- 限制显示结果的条数
- 从头显示指定条数
	- `limit n`
	- `limit 0, n`    (默认第一条记录的索引为0)
- 从指定位置开始显示指定条数
	- `limit n, m` 
		- 从第n+1行开始显示,直到第m行结束




## distinct   去重

```sql
select distinct field from tb ....
```
```sql
mysql> select post from employee;
+-----------+
| post      |
+-----------+
| doctor    |
| teacher   |
| teacher   |
| teacher   |
| teacher   |
| teacher   |
| teacher   |
| teacher   |
| sale      |
| sale      |
| sale      |
| sale      |
| sale      |
| operation |
| operation |
| operation |
| operation |
| operation |
+-----------+
18 rows in set (0.00 sec)

mysql> select distinct post from employee;
+-----------+
| post      |
+-----------+
| doctor    |
| teacher   |
| sale      |
| operation |
+-----------+
4 rows in set (0.00 sec)

```





## 对结果进行计算
- 可以把MySQL当作一个计算器,因此可以对输出结果进行计算处理
	- 比如根据平均值计算总值
- `+ - * / %`  --->  `加,减,乘,除,取余`





# 多表查询
- **多表查询语法结构**

```sql
select [distinct] <select_list> 
from <left_table>
<join type> join <right_table>
on <join_condition>
 where <where_condition>
group by <group_by_list>
having <having_condition>
order by <order_by_condition>
limit <limit_number>
```

- **关键字的执行顺序**
	- 从上到下，优先级从大到小
	- `<join type> join <right> table on <join_condition>`	
		-  多表连接优先级要在`where`之前，即`where`结果集通过对连接后的表进行约束
		-  `on` 的执行优先级在`join`之前，`<on_condition>`是连接表的前提条件


```sql
from
on		# on的执行优先级在join之前 
join		# join on 的执行优先级在where之前
where
group by
having
distinct		# distinct优先级在having之后
select 
orderby
limit


```


- 测试

```sql
# 数据库结构
create database TestDB;


# 表结构
CREATE TABLE table1
 (
     customer_id VARCHAR(10) NOT NULL,
     city VARCHAR(10) NOT NULL,
     PRIMARY KEY(customer_id)
 )ENGINE=INNODB DEFAULT CHARSET=UTF8;

 CREATE TABLE table2
 (
     order_id INT NOT NULL auto_increment,
     customer_id VARCHAR(10),
     PRIMARY KEY(order_id)
 )ENGINE=INNODB DEFAULT CHARSET=UTF8;



# 插入数据
 INSERT INTO table1(customer_id,city) VALUES('163','hangzhou');
 INSERT INTO table1(customer_id,city) VALUES('9you','shanghai');
 INSERT INTO table1(customer_id,city) VALUES('tx','hangzhou');
 INSERT INTO table1(customer_id,city) VALUES('baidu','hangzhou');

 INSERT INTO table2(customer_id) VALUES('163');
 INSERT INTO table2(customer_id) VALUES('163');
 INSERT INTO table2(customer_id) VALUES('9you');
 INSERT INTO table2(customer_id) VALUES('9you');
 INSERT INTO table2(customer_id) VALUES('9you');
 INSERT INTO table2(customer_id) VALUES('tx');
 INSERT INTO table2(customer_id) VALUES(NULL);
```


## **多表查询——连表查询**
- 连表查询就是将多张表通过共有的外键关系利用` left_table <join type> join right table on <condition>`将多张表组合成一张表之后，在对新的表进行之后的一系列where约束、group by分组以及having过滤等操作得出想要结果
	- 使用`<join type> join`最初得到的是一战笛卡儿积表，使用`on`条件筛选记录获取需要的表

```
# 简单来说，笛卡儿积就是一个嵌套for循环，以python为例
select * from tb1, tb2  
for  i  in tb2:
	for j in tb1:
		......


select * from tb1, tb2 where tb1.field  >  tb2.field
for  i  in tb2:
	for j in tb1:
		if tb1.field  >  tb2.field:
			......

```

- 举例 

```sql
SELECT * FROM table1 AS t1 
INNER JOIN table2 AS t2 
--------------------------------------------------------------------
customer_id  city      order_id  customer_id  
-----------  --------  --------  -------------
163          hangzhou         1  163          
9you         shanghai         1  163          
baidu        hangzhou         1  163          
tx           hangzhou         1  163          
163          hangzhou         2  163          
9you         shanghai         2  163          
baidu        hangzhou         2  163          
tx           hangzhou         2  163          
163          hangzhou         3  9you         
9you         shanghai         3  9you         
baidu        hangzhou         3  9you         
tx           hangzhou         3  9you         
163          hangzhou         4  9you         
9you         shanghai         4  9you         
baidu        hangzhou         4  9you         
tx           hangzhou         4  9you         
163          hangzhou         5  9you         
9you         shanghai         5  9you         
baidu        hangzhou         5  9you         
tx           hangzhou         5  9you         
163          hangzhou         6  tx           
9you         shanghai         6  tx           
baidu        hangzhou         6  tx           
tx           hangzhou         6  tx           
163          hangzhou         7  (NULL)       
9you         shanghai         7  (NULL)       
baidu        hangzhou         7  (NULL)       
tx           hangzhou         7  (NULL) 
```



### 连接表
- 内链接——`inner ... on condtion`
	- 按照on条件，链接成一张虚拟的表

```sql

SELECT * FROM table1 AS t1 
INNER JOIN table2 AS t2 
ON t1.customer_id=t2.customer_id;
---------------------------------------------------
customer_id  city      order_id  customer_id  
-----------  --------  --------  -------------
163          hangzhou         1  163          
163          hangzhou         2  163          
9you         shanghai         3  9you         
9you         shanghai         4  9you         
9you         shanghai         5  9you         
tx           hangzhou         6  tx    
```



- 左内链接——`left ...on condtion`
	- 按照on条件基础上，保留左表的记录

```sql

select * from table1 as t1 
left join table2 as t2 
on t1.customer_id=t2.customer_id;
----------------------------------------------------------------
customer_id  city      order_id  customer_id  
-----------  --------  --------  -------------
163          hangzhou         1  163          
163          hangzhou         2  163          
9you         shanghai         3  9you         
9you         shanghai         4  9you         
9you         shanghai         5  9you         
tx           hangzhou         6  tx           
baidu        hangzhou    (NULL)  (NULL)  
```

- 右内链接——`right  ... on condtion`
	- 按照on条件基础上，保留右表的记录

```sql
SELECT * FROM table1 AS t1 
RIGHT JOIN table2 AS t2 
ON t1.customer_id=t2.customer_id;
------------------------------------------------------------
customer_id  city      order_id  customer_id  
-----------  --------  --------  -------------
163          hangzhou         1  163          
163          hangzhou         2  163          
9you         shanghai         3  9you         
9you         shanghai         4  9you         
9you         shanghai         5  9you         
tx           hangzhou         6  tx           
(NULL)       (NULL)           7  (NULL)  
```


- 全外链接`union`
	- 在内连接的基础上增加左边有右边没有的和右边有左边没有的结果


```sql
SELECT * FROM table1 AS t1 
RIGHT JOIN table2 AS t2 
ON t1.customer_id=t2.customer_id
UNION
SELECT * FROM table1 AS t1
LEFT JOIN table2 AS t2 
ON t1.customer_id=t2.customer_id;
-------------------------------------------------------
customer_id  city      order_id  customer_id  
-----------  --------  --------  -------------
163          hangzhou         1  163          
163          hangzhou         2  163          
9you         shanghai         3  9you         
9you         shanghai         4  9you         
9you         shanghai         5  9you         
tx           hangzhou         6  tx           
(NULL)       (NULL)           7  (NULL)       
baidu        hangzhou    (NULL)  (NULL)   
```






- 子查询
	- 将以个查询结果当作另一个查询的参数使用，内层查询语句的查询结果，可以为外层查询语句提供查询条件。
	- **从要查出的字段所在的表开始，层层剥开，遇到什么就查询什么**
	- 注意：
		- 该查询结果在括号中不能用逗号结尾
		- 子查询中可以包含：IN、NOT IN、ANY、ALL、EXISTS 和 NOT EXISTS等关键字
		- 还可以包含比较运算符：= 、 !=、> 、<等算术符
	- `in`
		-  查询employee表，但dep_id必须在department表中出现过
		-  `select * from employee where dep_id in (select id from department);`
	- `= 、 !=、> 、<`  	
		- 查询平均年龄在25岁以上的部门名
			- `select id,name from department  where id in  (select dep_id from employee group by dep_id having avg(age) > 25);`
		- 查看技术部员工姓名
			- `select name from employee where dep_id in (select id from department where name='技术');`
		- 查看不足1人的部门名
			- `select name from department where id in  (select dep_id from employee group by dep_id having count(id) <=1);`
	- `exists/nt exists`返回一个布尔值
		- 当返回True时，外层查询语句将进行查询
		- 当返回False时，外能查询语句不会进行查询


### 举例
- 查询来自杭州，订单数小于2的客户

多表连接  <br>
```sql
# 使用多表连接
SELECT a.customer_id ,COUNT(a.customer_id)
FROM table1 AS a 
INNER JOIN table2 AS b 
ON a.customer_id=b.customer_id
WHERE city='hangzhou'
GROUP BY customer_id
HAVING customer_id < 2;
```


子查询  <br>
```sql
# 使用子查询
SELECT customer_id 
FROM table2 
GROUP BY  customer_id 
HAVING customer_id IN (SELECT customer_id FROM table1 WHERE city='hangzhou') AND COUNT(customer_id)<2；
```

	
## 联表查询与子查询的选择
