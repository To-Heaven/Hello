# 触发器trigger

## 结束符delimiter
- 关于`delimiter`
	- `delimiter` 用来声明每一行SQL语句结束时的结束符，在MySQL中，默认结束符是`;`
	- 在进行编写函数、触发器或存储过程的时候，我们需要在创建之前修改结束符
	
## 触发器是什么鬼
- 触发器**本质上是一个特殊的存储过程**，触发器的执行是由事件触发的


## 触发器的用处
- 定制用户对表进行增、删、改操作（没有查询）前后要触发的事件（行为）
- 加强数据的完整性约束和业务规则（或者说事务规则）
- 禁止或回滚对数据的修改

## 优点
- 保证数据的完整性

## 创建触发器
- 语法

```sql
CREATE
    [DEFINER = { user | CURRENT_USER }]
    TRIGGER trigger_name
    trigger_time trigger_event
    ON tbl_name FOR EACH ROW
    [trigger_order]
    trigger_body

trigger_time: { BEFORE | AFTER }        # 在指定时间之前/之后

trigger_event: { INSERT | UPDATE | DELETE }    #  事件内容

trigger_order: { FOLLOWS | PRECEDES } other_trigger_name

```

## 举例
- 使用触发器将用户执行失败的命令收集到一个列表中

```sql
create table cmd_tb(
id int primary key auto_increment,
cmd_name char(64),
sub_time datetime,
is_success enum('yes', 'no')
);

create table err_tb(
id int primary key auto_increment,
cmd_name char(64),
sub_time datetime
);

delimiter //
create 
    trigger tri_after_cmd
    after insert 
    on cmd_tb for each row
begin
    if new.is_success = 'no' then
    insert into err_tb values(new.cmd_name, new.sub_time);
    end if ;
end //
delimiter ;

insert into cmd_log(cmd_name,sub_time,user_name,is_success) values
('ls -l /etc | grep *.conf',now(),'root','no'),
('ps aux |grep mysqld',now(),'root','yes'),
('cat /etc/passwd |grep root',now(),'root','yes'),
('netstat -tunalp |grep 3306',now(),'egon','no');

```

## 关键字 new与old
- new 与 old 代表要操作的对应的数据
	- new代表新插入的数据，old是原来的数据
	- insert只会有new，代表着要插入的新纪录
	- delete只会有old，代表要删除的记录
	- update执行时是先删除原有数据，再插入新数据因此即有old又有new



## 常用结构

```sql
delimiter //
create trigger tri_name
    trigger_time trigger_event
    on tb_name for each row
begin
	[if 条件 then]
	执行语句1;
	执行语句2;
	[elseif 条件 then]
	执行语句;
	[else]
	执行语句;
	[end if ;]
end //
delimiter ;
```