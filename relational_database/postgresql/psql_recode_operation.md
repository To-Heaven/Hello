# 数据/记录操作

## 插入数据
#### 语法

```sql
insert into 表名(column1, column2, ..., columnx)
values
(value1, value2, ..., valuex),
(value1, value2, ..., valuex),
(value1, value2, ..., valuex);
```

- 插入的value要与column**位置一一对应**
- 当我们不明确的在表名后列出所有字段的时候，就会默认的将所有表中的字段作为要提交的values对应的字段。**应该明确的将要添加值的字段在表明后指出**


#### postgresql插入数据扩展
- 由于插入的每一个value要与前面的column位置一一对应，在插入大量数据的时候，对于某些记录的有些字段，可能是暂时没有值的，如果但是我们必须要传入一个值来占用着位置，保证该记录的后续value可以与各自对应的column对应上，这个值就是`DEFAULT`

###### 1. 对独立的字段使用default
- 在插入数据时，只需要将`default`放在对应的位置即可，如果该字段我们指定了默认值，那么就会按照我们指定的默认值填充，否则使用PostgreSQL默认的`NULL`填充

###### 2. 对整个记录行使用default
- 对整个行default意味着插入的整个行都会使用各自已经定义的default值填充

```sql
HINT:  There is a column named "gender" in table "info", but it cannot be referenced from this part of the query.
test=> insert into info (name, age, gender)
values
('haha', default, 'female');
INSERT 0 1
test=> select * from info;
   name   | age | gender 
----------+-----+--------
 stranger |  18 | male  
 ziawang  |  23 | male  
 haha     |     | female
(3 rows)

test=> insert into info default values;
INSERT 0 1
               
test=> select * from info;
   name   | age | gender 
----------+-----+--------
 stranger |  18 | male  
 ziawang  |  23 | male  
 haha     |     | female
 stranger |     | 
(4 rows)
```


## 修改数据
- 要更新一条或多条数据，再更新前我们需要做些准备
	1. 要被更新数据的表
	2. 要被更新的字段
	3. 要被更新的行
	4. 每个字段的新的值

#### 语法

- 更新单个字段值

```
update 表名 set 字段名=表达式 where 条件;
```

- 更新多个字段值

```
update 表名 set column1=value1, column2=value2 where 条件;
```


- 表达式可以是一个新的value，也可以是字段现有value构成的表达式

```
test=> create table goods(
id int,
good_name varchar(20) not null,
price int check(price > 0));
CREATE TABLE
test=> insert  into goods(id, good_name, price)
test-> values
test-> (1, 'apple', 10),
test-> (2, 'banana', 5);
INSERT 0 2
test=> update goods set price=price*2 where id=2;
UPDATE 1
test=> select good_name, price from goods;
 good_name | price 
-----------+-------
 apple     |    10
 banana    |    10
(2 rows)

test=> 

```

## 删除数据
- 和更新数据一样，删除数据也需要一些准备
	1. 要删除的数据在哪一个表？
	2. 要删除的数据是那一个字段的值
	3. 要删除哪些记录

```
delete from 表名 where 条件;
```

- **请不要随意使用下面这条命令**
	- `delete from 表名;`

```
test=> select * from goods;
 id | good_name | price 
----+-----------+-------
  1 | apple     |    10
  2 | banana    |    10
(2 rows)

test=> delete from goods where good_name='banana';
DELETE 1
test=> select * from goods;
 id | good_name | price 
----+-----------+-------
  1 | apple     |    10
(1 row)

test=> 

```