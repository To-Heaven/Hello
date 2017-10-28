## 数据库命名
1. 全部小写
2. 不能使空字符串
3. 不能含有空格、`.`、`$`、`/`、`\`、`\0`(代表空字符)
4. 最多64字节


## 内置数据库
#### admin
- 首先，MongoDB中没有"root"用户这一说，要创建具有管理权权限的用户，需要将用户添加到`admin`数据库下，该用户会继承所有数据库的权限
- 执行服务器端的命令，比如关闭服务器和列出所有数据库

#### local
- 存储本地单台服务器的任意集合。这个数据库不会被复制

#### test
- 留给我们练习用




## 数据库操作
#### 创建数据库
- `use 数据库名`
	- 当数据库不存在时，创建数据库
		- mongodb中数据库是隐式创建的
	- 当数据库存在时，切换到该数据库

#### 查看数据库
> show dbs 不会列出空数据库

- `show dbs`
	- 查看当前用户下的数据库

- `db`
	- 查看用户当前所在的数据库

#### 删除数据库
- 切换到当前使用的数据库下，再执行删除命令
- `db.dropDatabase()`

```sql
> show dbs;
admin  0.000GB
first  0.000GB
local  0.000GB
test   0.000GB
> db
test
> use first
switched to db first
> db.dropDatabase()
{ "dropped" : "first", "ok" : 1 }
> show dbs;
admin  0.000GB
local  0.000GB
test   0.000GB
>
```


