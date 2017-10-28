
## bin(binary)目录
#### 核心
- mongo: 客户端(相当于mysql)
- mongod: 服务端(相当于mysqld)
- mongos: 查询路由器、集群时用

#### 数据导出导入
- mongoexport: 导出易识别的json文档、csv文档、tsv格式
- mongoimport: 导入json文档、csv文档、tsv文档


#### 导出导入
- bsondump: 导出Bson结构
- mongorestore: 数据库整体导入成bson
- mongodump: 整体数据库导出(二进制，相当于mysqldump)

#### 诊断工具
- mongostats
- mongotop
	- 查看服务器运行状态
- mongosniff


## 使用mongodb
- `./bin/mongod --dbpath/path/to/database --logpath /path/to/log --fork --port xxxx`
	- `--dbpath` 数据存储目录
	- `--logpath`日志存储目录
	- `--port`  运行进程端口
	- `fork`后台运行

- 连接至服务器
	- `./bin/mongo`



## 用户配置
#### 用户登陆
- 命令格式

```
mongodb://username:password@host[port=27017]/database[?options]
```


#### 管理员权限
1、mongodb是没有默认管理员账号，所以要先添加管理员账号，在开启权限认证。
2、切换到admin数据库，添加的账号才是管理员账号。
3、用户只能在用户所在数据库登录，包括管理员账号。
4、管理员可以管理所有数据库，但是不能直接管理其他数据库，要先在admin数据库认证后才可以。这一点比较怪

####
- `db.help()`


## 注意
- mongodb非常占用磁盘空间（刚启动后3~4G）
	- 使用`--smallfiles`参数启动可以减少磁盘占用空间