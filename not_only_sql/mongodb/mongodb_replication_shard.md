# 复制集与分片公用

- 对于一个片而言，它既可以是一个普通的mongodb服务器，也可以是一个复制集中的primary server。在shard分片的部署过程中，完全可以将一个复制集看作普通的分片服务器使用


## 实现
#### 需求分析 
- 现在为数据进行分片，部署结构如下
	- mongos服务器（普通mongod服务器）
	- configsvr服务器（普通服务器），放置meta元信息
	- 分片服务器1（复制集）
	- 分片服务器2（复制集）

- 注意：
	- 在生产环境中，不推荐只使用一个configsvr服务器

#### 1. 创建复制集


#### 


#### 注意
- 添加复制集到分片服务器上的时候，需要声明复制集
	- `rs/192.168.202.1:27019`
