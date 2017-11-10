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
 .bin/mongod --dbpath /home/m17 --logpath /home/mlog/m17.log --fork --port 27017 --replSet rep1 --smallfiles

 .bin/mongod --dbpath /home/m18 --logpath /home/mlog/m18.log --fork --port 27018 --replSet rep1	--smallfiles

 .bin/mongod --dbpath /home/m19 --logpath /home/mlog/m19.log --fork --port 27019 --replSet rep1 --smallfiles
```


##### 3.配置
- 连接上启动的任意一个实例服务端，进入admin数据库下

```
var repconf = {
_id: "rep1"
members: [
		{_id:0, host: '192.168.1.202:27017'},
		{_id:1, host: '192.168.1.202:27018'},
		{_id:2, host: '192.168.1.202:27019'},
	]
}
```

##### 4.初始化配置
- 对创建的配置变量进行初始化
- `rs.initiate(repconf)`

###### 5.查看复制集信息
- `rs.status();`


### 管理复制集
##### 添加节点
- 添加节点之前需要使用`rs.reconfig(repconfig)`重新配置复制集，其中repconfig中要包含要添加的实力服务端的配置。步骤如下

1. 创建配置

```
var repconf = {
_id: "rep1",
members: [
		{_id:0, host: '192.168.1.202:27017'},
		{_id:1, host: '192.168.1.202:27018'},
		{_id:2, host: '192.168.1.202:27019'},
		{_id:3, host: '192.168.1.202:27020'},
		{_id:4, host: '192.168.1.202:27021'},
	]
}
```

2. 重新配置

```
rs.reconfig(repconf)
```



##### 删除节点
- `rs.remove('192.168.1.202:27019')`

##### 从secondary服务器中查看同步的数据
- 往复制集中的primary服务器进行写入操作的时候，数据会同步到复制集其他的服务器中，此时我们切换到secondary服务器中去查询在primary中插入的数据时，会查看失败，因为slave默认情况下时不允许读写，只与primary保持通信的。
- 遇到这种情况，我们需要在secondary中使用`rs.slaveOk()；`，然后再查询数据就能正常看见同步的数据了



### 复制集中服务器的自动切换
- 当我们在primary服务器下使用`db.shutdownServer()`的时候，primary服务器会被强行终止掉。这个时候，我们切换到其他secondary服务器下使用`rs.status()`方法会发现，原来的primary服务器的状态信息中，`"heath": 0, "stateStr": "(not reachable/healthy)"`，并且原来`_id=1`的secondary服务器的状态信息`"heath": 1, "stateStr": "PRIMARY"`，即该secondary服务器自动成为了primary服务器来与客户端进行数据交互了。
- 注意： `db.shutdownServer()`命令要在admin下使用

### 创建shell脚本




