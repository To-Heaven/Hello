# replication set 复制集
- 所谓复制集就是指多台服务器维护相同的数据库副本，提高服务器的可用性
- 主服务器负责与客户端进行交互进行数据的读写，同时其他服务器还会从主服务器那里同步数据，主服务端与其他服务端之间的数据集是相同的，如果主服务器出现了问题，读写任务就会交由其他服务器来完成。（主服务器与服务器之间，其他服务器之间都是保持着相互通信的）



```
				 client	
			   		|
			   write|read
					|
		 ———-————primary—————————
		|replication			 |replication
		|						 |
	secondary <——————————————> secondary
				 connection

```

### 配置复制集
##### 1.准备: 为节点创建目录
- `mkdir /home/m17 /home/m18 /home/m19 /home/mlog/ `

##### 2.启动: 启动三个实例，并声明实例属于某一个复制集
- 指令参数`--replSet rep`rep为复制集名称
- help文档如下

```
 --replSet arg                         arg is <setname>[/<optionalseedhostlist
                                        >]

 --smallfiles                          use a smaller default file size
```



```
[root@host mongodb-linux-x86_64-3.4.10]# pkill -9 mongo
[root@host mongodb-linux-x86_64-3.4.10]# ./bin/mongod --dbpath /home/m17 --logpath /home/mlog/m17.log --fork --port 27017 --replSet res --smallfiles
about to fork child process, waiting until server is ready for connections.
forked process: 14200
child process started successfully, parent exiting

[root@host mongodb-linux-x86_64-3.4.10]# ./bin/mongod --dbpath /home/m18 --logpath /home/mlog/m18.log --fork --replSet res --port 27018 --smallfiles
about to fork child process, waiting until server is ready for connections.
forked process: 14228
child process started successfully, parent exiting

[root@host mongodb-linux-x86_64-3.4.10]# ./bin/mongod --dbpath /home/m19 --logpath  /home/mlog/m19.log --fork --port 27019 --replSet res --smallfiles
about to fork child process, waiting until server is ready for connections.
forked process: 14257
child process started successfully, parent exiting

```


##### 3.配置
- 连接上启动的任意一个实例服务端，进入admin数据库下
	- **注意**：配置变量repconf指定的复制集`_id`要和上面创建的复制集名称相同`--replSet 复制集名`

```
> var repl = {
... _id: "res",
... members:[
... {_id: 0, host:"172.96.203.6:27017"},
... {_id: 1, host: "172.96.203.6:27018"},
... {_id: 2, host: "172.96.203.6:27019"}]};

```

##### 4.初始化配置
- 对创建的配置变量进行初始化
- `rs.initiate(repconf)`

###### 5.查看复制集信息
- `rs.status();`


```bash
res:PRIMARY> rs.status()
{
	"set" : "res",
	"date" : ISODate("2017-11-13T12:40:29.427Z"),
	"myState" : 1,
	"term" : NumberLong(1),
	"heartbeatIntervalMillis" : NumberLong(2000),
	"optimes" : {
		"lastCommittedOpTime" : {
			"ts" : Timestamp(1510576823, 1),
			"t" : NumberLong(1)
		},
		"appliedOpTime" : {
			"ts" : Timestamp(1510576823, 1),
			"t" : NumberLong(1)
		},
		"durableOpTime" : {
			"ts" : Timestamp(1510576823, 1),
			"t" : NumberLong(1)
		}
	},
	"members" : [
		{
			"_id" : 0,
			"name" : "172.96.203.6:27017",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 1283,
			"optime" : {
				"ts" : Timestamp(1510576823, 1),
				"t" : NumberLong(1)
			},
			"optimeDate" : ISODate("2017-11-13T12:40:23Z"),
			"electionTime" : Timestamp(1510576701, 1),
			"electionDate" : ISODate("2017-11-13T12:38:21Z"),
			"configVersion" : 1,
			"self" : true
		},
		{
			"_id" : 1,
			"name" : "172.96.203.6:27018",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 139,
			"optime" : {
				"ts" : Timestamp(1510576823, 1),
				"t" : NumberLong(1)
			},
			"optimeDurable" : {
				"ts" : Timestamp(1510576823, 1),
				"t" : NumberLong(1)
			},
			"optimeDate" : ISODate("2017-11-13T12:40:23Z"),
			"optimeDurableDate" : ISODate("2017-11-13T12:40:23Z"),
			"lastHeartbeat" : ISODate("2017-11-13T12:40:27.874Z"),
			"lastHeartbeatRecv" : ISODate("2017-11-13T12:40:28.541Z"),
			"pingMs" : NumberLong(0),
			"syncingTo" : "172.96.203.6:27017",
			"configVersion" : 1
		},
		{
			"_id" : 2,
			"name" : "172.96.203.6:27019",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 139,
			"optime" : {
				"ts" : Timestamp(1510576823, 1),
				"t" : NumberLong(1)
			},
			"optimeDurable" : {
				"ts" : Timestamp(1510576823, 1),
				"t" : NumberLong(1)
			},
			"optimeDate" : ISODate("2017-11-13T12:40:23Z"),
			"optimeDurableDate" : ISODate("2017-11-13T12:40:23Z"),
			"lastHeartbeat" : ISODate("2017-11-13T12:40:27.874Z"),
			"lastHeartbeatRecv" : ISODate("2017-11-13T12:40:28.542Z"),
			"pingMs" : NumberLong(0),
			"syncingTo" : "172.96.203.6:27017",
			"configVersion" : 1
		}
	],
	"ok" : 1
}
res:PRIMARY> 


```


### 管理复制集
##### 添加节点
- 添加节点使用`rs.add('192.168.1.202:27020')`
 



##### 删除节点
- `rs.remove('192.168.1.202:27019')`

##### 从secondary服务器中查看同步的数据
- 往复制集中的primary服务器进行写入操作的时候，数据会同步到复制集其他的服务器中，此时我们切换到secondary服务器中去查询在primary中插入的数据时，会查看失败，因为slave默认情况下时不允许读写，只与primary保持通信的。


```bash
[root@host mongodb-linux-x86_64-3.4.10]# ./bin/mongo 172.96.203.6:27018
MongoDB shell version v3.4.10
connecting to: 172.96.203.6:27018
MongoDB server version: 3.4.10

res:SECONDARY> show dbs;
2017-11-13T07:48:58.472-0500 E QUERY    [thread1] Error: listDatabases failed:{
	"ok" : 0,
	"errmsg" : "not master and slaveOk=false",
	"code" : 13435,
	"codeName" : "NotMasterNoSlaveOk"
} :
_getErrorWithCode@src/mongo/shell/utils.js:25:13
Mongo.prototype.getDBs@src/mongo/shell/mongo.js:62:1
shellHelper.show@src/mongo/shell/utils.js:781:19
shellHelper@src/mongo/shell/utils.js:671:15
@(shellhelp2):1:1

```

- 遇到这种情况，我们需要在secondary 服务器中中使用`rs.slaveOk()；`，然后再查询数据就能正常看见同步的数据了


```
[root@host mongodb-linux-x86_64-3.4.10]# ./bin/mongo 172.96.203.6:27018
MongoDB shell version v3.4.10
connecting to: 172.96.203.6:27018
MongoDB server version: 3.4.10

res:SECONDARY> rs.slaveOk();
res:SECONDARY> show dbs;
admin  0.000GB
local  0.000GB
test   0.000GB
res:SECONDARY> use test
switched to db test
res:SECONDARY> show tables;
col
res:SECONDARY> db.col.find();
{ "_id" : ObjectId("5a0994028f94a659dad6295b"), "name" : "ziawang" }
res:SECONDARY> 

```

### 复制集中服务器的自动切换
- 当我们在primary服务器下使用`db.shutdownServer()`的时候，primary服务器会被强行终止掉。这个时候，我们切换到其他secondary服务器下使用`rs.status()`方法会发现，原来的primary服务器的状态信息中，`"heath": 0, "stateStr": "(not reachable/healthy)"`，并且原来`_id=1`的secondary服务器的状态信息`"heath": 1, "stateStr": "PRIMARY"`，即该secondary服务器自动成为了primary服务器来与客户端进行数据交互了。
- 注意： `db.shutdownServer()`命令要在admin下使用

### 创建shell脚本




