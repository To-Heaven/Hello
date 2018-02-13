# foreign key
## 多对一(关联表的多条记录对应被关联表的一条记录)

![多对一](https://github.com/ZiaWang/Hello/blob/master/picture/servers_clients.png?raw=true)

- 注意
	1.  先创建主键表，被关联的字段（references）要保证是唯一的
	2.  再创建外键表，关联的字段，一定要保证是可以重复的，不能使用unique
	3.  表类型必须是InnoDB引擎，因为在MySQL中只有InnoDB引擎才只支持外键约束
	4.  使用 `on update cascade ` 和`on delete cascade`进行级联操作
		- 此时要将`on delete cascade`和`on update cascade`放在生成外键的语句中（即和foreign key 关键字在同一个语句中，不要使用逗号隔离）

```sql
create table servers(
id int primary key auto_increment,
address char(15),
port int
);


create table clients(
id int primary key auto_increment,
adress char(15),
server_id int,      # 这里要有逗号
constraint fk_cs foreign key(server_id) references servers(id)
on update cascade
on delete cascade
);

```


![一对一](https://github.com/ZiaWang/Hello/blob/master/picture/server_client.png?raw=true)


## 一对一
- 一对一要求的是外键表和主键表之间是互相的一一对应关系，不能出现重复
	- 在上例，多个客户端对应一个服务端，但是多个服务端却不能对应一个客户端，这是多对一
	- 到了一对一就是，一个客户端只能对应一个服务端，并且一个服务端只能对应一个客户端
	
- 注意
	- 外键必须是唯一的，不能重复

```sql
create table server(
id int primary key auto_increment,
addr char(15),
port int
);



create table client(
id int primary key auto_increment,
addr char(15),
server_id int,
unique key(server_id),
constraint fk_cs foreign key(server_id) references server(id)
on delete cascade
on update cascade
);

```




## 多对多

![多对多](https://github.com/ZiaWang/Hello/blob/master/picture/book_author.png?raw=true)

- 多对多即主键表和外键表之间都满足双向的多对一的关系
	- 以书我和作者为例
		- 多个书可以对应一个作者，多个作者可以对应一本书
- 注意：
	- 应该选择第三张表用于存放每一本书与一位作者的关系
	- 每一本书与作者的关系不需要再该关系表中重复，因此使用联合唯一


- 在多张表中多对多

```sql
create table book(
id int primary key auto_increment,
name varchar(10)
);

create table author(
id int primary key auto_increment,
name varchar(20)
);

create table relation(
id int primary key auto_increment,
book_id int,                # 注意要有逗号
foreign key(book_id) references book(id)
on delete cascade
on update cascade,
author_id int,		# 注意要有逗号
foreign key(author_id) references author(id)
on delete cascade
on update cascade
);
 
```

 