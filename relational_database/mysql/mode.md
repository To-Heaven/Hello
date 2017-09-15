# MySQL mode
- 怎么设置MySQL mode
	- 在mysql交互命令行中输入对应命令如下
	- `set sql_mode = 'ONLY_FULL_GROUP_BY'`

- 查看当前设置的MySQL mode 
	- `select @@global.sql_mode;` 
	- ps
		- 如果向去掉sql_mode中的某个模式值，可以将除了该sql_mode之外的其他值复制粘贴重新使用set 命令赋值给sql_mode


## only_full_group_by
> MySQL5.7版本默认该模式

-   在`ONLY_FULL_GROUP_BY模式下，target list中的值要么是来自于聚集函数的结果，要么是来自于group by list中的表达式的值`，否则就会出错，这是为了避免得到语义不明确的查询结果
-   比如

```sql
mysql> create database test charset utf8mb4;
mysql> use test;
mysql> create table tt(id int,count int);
mysql> insert into tt values(1,1),(1,2),(2,3),(2,4);
mysql> select * from tt group by id;
+------+-------+
| id   | count |
+------+-------+
|    1 |     1 |
|    2 |     3 |
+------+-------+
2 rows in set (0.00 sec)

```
