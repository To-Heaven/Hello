# 视图

## 视图是什么鬼
##### 本质
- **视图本质上就是一个虚拟表，它是我们使用SQL语句获取到的一个数据集，在数据库中，它只有表的结构，其数据来自于其他表**
	- 我们可以为这个动态数据集命名。当我们在复杂的联表查询时，就可以将该结果集作为表来使用，而不需要再次使用复杂的SQL语句去获取。

##### 进一步解释
- 数据查询中，使用子查询时，我们经常需要先从另一张表中获取数据，再拿着这些数据进行子查询。如果每次子查询都要用到同一个表中的数据，为了避免重复造轮子，我们可以将这个子查询得到的结果存放在虚拟表中。
	- 这里的`存放`是为了便于理解，其实**数据并没有存放在虚拟表中**，数据库中只存在该虚拟表的结构

###### 视图存放在数据库中 
- 当我们通过一条SQL命令创建一个视图（虚拟表）时，在对应的数据库下会出现以视图名称为文件名，后缀为`.frm`的文件。每一个数据库中创建的表都会有一个`.frm`文件，用来存放表的结构（字段信息，表信息）
- 视图创建完毕之后只会产生一个`.frm`文件，而对于一个表来说，以InnoDB引擎为例，创建表的同时会创建存放表结构的文件`.frm`和存放表数据的文件`.idb`。即，视图是没有存放数据的文件的，因此说视图是一张虚拟表
	- 视图本身没有数据，只是通过执行相应的select语句来获得相应的数据



## MySQL视图操作
- 创建视图

```sql
    [OR REPLACE]
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = { user | CURRENT_USER }]
    [SQL SECURITY { DEFINER | INVOKER }]
    VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]

# -----------------------------------------------------------------------
# 从单个表中创造视图
create view customer_view as 
select id, name from customer 
where age<30;


# ----------------
# 利用多个表创造视图
create view user_dep as 
select user.id as uid, user.name as uname, dep.id as did, dep.name as depname 
from user left join dep;   # 如果两张表存在外键要记得去除充重复字段
```

- 删除视图

```sql
DROP VIEW [IF EXISTS]
    view_name [, view_name] ...
    [RESTRICT | CASCADE]
# ----------------------------------------------------------------------------
drop view customer_view;

```

- 修改视图

```sql
ALTER
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = { user | CURRENT_USER }]
    [SQL SECURITY { DEFINER | INVOKER }]
    VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
# ----------------------------------------------------------------------
alter view customer_view as 
select id, name, email from customer
where age >18; 

``` 

## 视图的操作
- 视图是一张虚拟表，其操作和操作表语法相同

```sql
select * from customer_view;
update customer_view set
name ='haha'
where id = 2;
```



## 视图注意事项
#### 效率
- 使用视图虽然可以帮助我们避免重复造轮子，但是会降低查询效率。
- 工作中，数据库管理员与程序员之间存在沟通成本，当我们需要修改一个视图的结构的时候，往往花费的时间比较久
#### 数据问题
- 视图是没有存放数据的，它通过调用视图的SQL语句去创建视图的表对应的文件中去获取数据。
	- 视图也包含一系列带有名称的行和列数据，行和列数据来自由定义视图的查询所引用的表，并且在引用视图时动态生成
-  如果视图创建自多个表，在选择字段的时候，要给相同的名称如`id`, `name`去别名，防止重复，而且对于存在由外键关系的两个表，创建视图可能会出现重复的字段，应去除重复字段
#### 数据的修改
-  对于从单个表创建而来的视图，对视图的字段属性和数据值进行修改的时候会同时修改源表对应文件中的字段和数据值
-  但是对于从多个表中创建的视图，对视图的字段属性和数据值的修改会报错
-  如果源表中的数据被修改了，那么视图查询对应的数据也会修改
-  如果源表中的字段被修改了，如果该字段是视图的一部分，那么视图在查询数据的时候可能会出错
	-  不应该修改视图中的记录，而且在涉及多个表的情况下是根本无法修改视图中的记录的


## 视图的优缺点
#### 优点
- 简化用户操作，用户可以将注意力集中在所关心的数据上
- 使用户从多角度看待同一数据（多个用户）
- 能够对机密数据提供安全保护
	- 为不同用户定义不同视图
- 清晰表达查询

#### 缺点
- 效率低
- 耦合性较强，不利于SQL语句的扩展

## 数据准备
 
```sql
CREATE TABLE t_employee(
        ID INT  PRIMARY KEY  AUTO_INCREMENT,
        NAME CHAR(30) NOT NULL,
        SEX  CHAR(2) NOT NULL,
        AGE INT NOT NULL,
        DEPARTMENT CHAR(10) NOT NULL,
        SALARY  INT NOT NULL,
        HOME CHAR(30),
        MARRY CHAR(2) NOT NULL DEFAULT  '否',       
        HOBBY CHAR(30)
 );

INSERT INTO learning.t_employee(ID, NAME, SEX, AGE,DEPARTMENT, SALARY, HOME, MARRY, HOBBY) VALUES(NULL,'小红','女',20,'人事部','4000','广东','否','网球');
INSERT INTO learning.t_employee(ID, NAME, SEX, AGE,DEPARTMENT, SALARY, HOME, MARRY, HOBBY) VALUES(NULL,'明日','女',21,'人事部','9000','北京','否','网球');
INSERT INTO learning.t_employee(ID, NAME, SEX, AGE,DEPARTMENT, SALARY, HOME, MARRY, HOBBY) VALUES(NULL,'天天','男',22,'研发部','8000','上海','否','音乐');
INSERT INTO learning.t_employee(ID, NAME, SEX, AGE,DEPARTMENT, SALARY, HOME, MARRY, HOBBY) VALUES(NULL,'大大','女',23,'研发部','9000','重庆','否','无');
INSERT INTO learning.t_employee(ID, NAME, SEX, AGE,DEPARTMENT, SALARY, HOME, MARRY, HOBBY) VALUES(NULL,'王下','女',24,'研发部','9000','四川','是','足球');
INSERT INTO learning.t_employee(ID, NAME, SEX, AGE,DEPARTMENT, SALARY, HOME, MARRY, HOBBY) VALUES(NULL,'无名','男',25,'销售部','6000','福建','否','游戏');
INSERT INTO learning.t_employee(ID, NAME, SEX, AGE,DEPARTMENT, SALARY, HOME, MARRY, HOBBY) VALUES(NULL,'不知道','女',26,'销售部','5000','山西','否','篮球');
``` 
