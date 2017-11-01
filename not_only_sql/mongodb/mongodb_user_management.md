## 用户管理
- mongodb的所有服务器配置和管理，都是在admin数据库下面进行的
	- `use admin`
- 当进入admin数据库下的时候，相当于进入了超级管理员模式
- mongodb的用户是以数据库为单位来建立的，每一个数据库都有自己的管理员
- 设置用户时，需要先在admin数据库下建立超级管理员，这个管理员登录之后相当于超级管理员

#### 查看用户
- `db.system.user.find()`

#### 添加用户
- `db.createUser({user:str_username, pwd:str_pwd, roles:[{role:str_role, db:str_db}, {role:str_role, db:str_db}]})`
	- roles: 指定用户的角色，角色字段的值有两种"内置角色"和用户"自定义角色"。如果roles在创建用户的时候不指定，那么用户的角色为空
		- 内置角色
			1. 数据库用户角色
				1. read: 允许用户读取指定数据库
				2. readWrite: 允许用户读写指定数据库
			2. 数据库管理角色
				1. dbAdmin: 允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
				2. dbOwner: 
				3. userAdmin: 允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
			3. 集群管理角色
				1. clusterAdmin: 只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
				2. lusterManager: 
				3. clusterMonitor: 
				4. hostManager: 
			4. 备份恢复角色
				1. backup
				2. restore
			5. 所有数据库角色：
				1. readAnyDatabase: 只在admin数据库中可用，赋予用户所有数据库的读权限
				2. readWriteAnyDatabase: 只在admin数据库中可用，赋予用户所有数据库的读写权限
				3. userAdminAnyDatabase: 只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
				4. dbAdminAnyDatabase: 只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
			6. 超级用户角色
				1. root: 只在admin数据库中可用。超级账号，超级权限
				- 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）
			7. 内部角色：__system


```
> use admin
switched to db admin
> db.createUser({
... user:"admin",
... pwd:"password",
... roles:["root"]});
Successfully added user: { "user" : "admin", "roles" : [ "root" ] }
```


#### 删除用户
- `db.removeUser(str_user)`


#### 修改用户
###### 修改用户密码
- `db.changeUserPassword(str_user, str_newPassword)`


## mongodb登陆验证

#### 服务端开启验证
- 在开启服务端的时候，添加`--auth`参数可以保护数据库中的数据

- `db --dbpath str_path --auth`


#### 客户端登陆验证

- 开启验证时，如果我们不经过用户验证就直接操作数据库会抛出错误

```
> show tables;
2017-11-01T18:49:29.135+0800 E QUERY    [thread1] Error: listCollections failed: {
        "ok" : 0,
        "errmsg" : "not authorized on test to execute command { listCollections: 1.0, filter: {} }",
        "code" : 13,
        "codeName" : "Unauthorized"
} :
_getErrorWithCode@src/mongo/shell/utils.js:25:13
DB.prototype._getCollectionInfosCommand@src/mongo/shell/db.js:807:1
DB.prototype.getCollectionInfos@src/mongo/shell/db.js:819:19
DB.prototype.getCollectionNames@src/mongo/shell/db.js:830:16
shellHelper.show@src/mongo/shell/utils.js:774:9
shellHelper@src/mongo/shell/utils.js:671:15
@(shellhelp2):1:1
>
```

###### 用户登陆mongodb验证步骤
1. 切换到`admin`数据库下
2. 使用验证函数`db.auth(str_user, str_pwd)`
	- 如果验证成功，就会返回一个`1` ，否则就会返回0，并且抛出错误

```
> db.auth("ziawang", "xxx");
Error: Authentication failed.
0
> db.auth("ziawang", "pass");
1
```