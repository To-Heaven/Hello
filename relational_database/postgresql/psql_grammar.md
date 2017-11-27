# PostgreSQL语法规范

## 语句
1. 以`;`结尾
2. 变量标识符和关键字对大小写不敏感

## 标识符
1. 必须以字母或下划线开头
2. 可以包含字母、下划线、`$`、数字。
3. `$`不要出现在注释中	


## 引号
1. 在插入数据的时候，要存放的数据要用**单引号**包含起来

```
postgres=# insert into info(name, signup_date) values("ziawang", "2017-12-5");ERROR:  column "ziawang" does not exist
LINE 1: insert into info(name, signup_date) values("ziawang", "2017-...
                                                   ^
postgres=# insert into info(name, signup_date) values('ziawang', "2017-12-5"); 
ERROR:  column "2017-12-5" does not exist
LINE 1: ...rt into info(name, signup_date) values('ziawang', "2017-12-5...
                                                             ^
postgres=# insert into info(name, signup_date) values('ziawang', '2017-12-5);
postgres'# 

```