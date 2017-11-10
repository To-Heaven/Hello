# shard分片

## 介绍

### 复制集与shard分片
- 复制集上的每一个服务器节点储存的数据都是相同的，相当于备份。与复制集不同，当数据量非常大的时候，我们可以使用shard分片将数据到多个节点上去储存，即把数据分布在不同的服务器上。

#### 分片工作原理
###### mongos
- 本质上是一个路由，我们只需要将对数据操作的请求交给mongos，mongos会先找到configsvr，询问该数据在哪一个shard片上，根据config上的meta信息寻找到我们需要的数据并返回

###### configsvr
- 不存储真正的数据，存储的是meta信息及“某条数据在哪一个片上”的信息


#### 分片工作流程
1. 建立多个节点的服务

#### 分片条件
1. 要有N(N>=2)个mongod服务器作为片节点
2. 要有configsvr维护meta信息
	- meta信息就是数据的分片信息
3. 设定好数据的分片规则，这样configsvr才能够维护
4. 启动mongos作为路由


## 分片的实现


#### 准备节点
1. 创建文件夹
	- `mkdir -p /home/m17 /home/m18 /home/m20 /home/mlog`
	- `m17`
	- `m18`
	- `m20`
	- `mlog`
2. 启动
	- 启动节点服务器
		- `./bin/mongod --dbpath /home/m17/ --logpath /home/mlog/m17.log --fork --port 27017 --smallfiles`
		- `./bin/mongod --dbpath /home/m18/ --logpath /home/mlog/m18.log --fork --port 27018 --smallfiles`
	- 启动configsvr(使用--configsvr参数告诉mongod该服务器是configsvr服务器，用来保存meta信息)
		- `./bin/mongod --dbpath /home/m20/ --logpath /home/mlog/m20.log --fork --port 27020 --configsvr`
	- 启动mongos路由(启动mongos路由我们需要使用mongos而非mongod来完成)
		- `--configdb` 指向的是configsvr服务器，mongos需要通过此参数来去configsvr上获取数据所在的片信息，参数的值是configsvr服务器所在的主机IP及端口
		- `./bin/mongos --logpath /home/mlog/m30.log --port 30000 --comfigdb 192.168.1.202.27020 --fork`

3. 配置片节点
	1. 配置片节点是通过mongos完成的，因此我们首先需要连接上mongos服务器
		- `./bin/mongo --port 30000`
	2. 使用`sh.addShard(shard)`添加片节点。
		- shard参数是用引号包含的字符串，参数值是每一个分片节点所在服务器的IP地址和端口（因为IP+端口可以标识全世界范围内运行在一台主机上的唯一的进程）
		- `sh.addShard(192.168.1.202:27017)`
		- `sh.addShard(192.168.1.202:27018)`  
		- 使用`sh.status()`查看分片状态

> 使用sh.help()来获取shard操作的相关函数


4. 设定数据的分片规则
	- 如果不设定，那么数据将只会在一台节点服务器上储存，不会出现分片的效果。使用`sh.status()可以查看mongos状态(partitioned参数)`
	1. 首先要声明实现分片的库
		- `sh.enableSharding(str_dbName)`，如果dbName不存在，会创建该数据库
	2.  然后声明具体的分片的集合（表）
		- `sh.shardCollection('dbName.collectionName', {field: 1})`
			- field非常关键，它是collection的一个字段，mongo会根据field的值来计算应该将数据分到哪一个片上。这个field就做"片键",shard key。
			- 一般来说，都是用主键作为shard key


#### 注意
- mongodb的分片不是按照单个document的级别将数据散落在各个片上，而是n个document形成一个`chunk`块，优先放在某一个片上，当这个偏上的chunk的区别度较高于另一个chunk块的时候，才会把本片上的chunk转移到其他片上，以chunk为单位维护片之间的数据均衡
	- 我们可以修改chunk size（默认64m）
		- `use config`
		- `db.settings.save({_id:'chunksize', value:4})`，这里修改成4m
	- 当chunk数量较多的时候，使用`sh.status()`mongo不会显示所有的chunk，可以使用verbose force print


- mongodb优先向0某一个片上插入数据，当该片上chunk较多，倒是多个片之间的chunk数目失衡的时候，就会移动chunk到其他片上，因此shard的片之间有chunk来回移动的现象，对于数据量比较大的网站来说，为了保证各个片之间chunk数量的均衡，**将会带来严重的IO，增加服务器之间的负担**
- 因此我们可以手动预先分片，来实现将指定数量的数据形成一个chunk，预先将数据分配指定数量的chunk中，将这些chunk预先分配到不同的片上，不再来回移动