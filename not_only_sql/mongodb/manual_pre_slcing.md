## 手动预先分片


#### 1. sh.shardCollection(fullname, shard_key)
- 手动分片前需要使用`shardCollection`告诉mongos我们要创建的shard_key


#### 2. sh.splitAt(fullName, middle)
- 需要提前使用个此函数告诉mongos
- fillName: 要被切分的对象
- middle: 字段名（被切分对象的shard key），用来指定分片条件
- 比如
	- `sh.splitAt("shop.user", {userid: i*1000})`表示对shop数据库下的user集合进行分片，按照userid每1000个文档分片一次
- 使用splitAt的时候，可以配合for循环来指定分片，每一次for循环内都执行一次`splitAt`，创建一个chunk

```
for (var i=1; i<=40; i++){
	sh.splitAt("userinfo.vip", {userid: i*1000})
}
```

- 创建完chunk之后，chunk将会均匀的移动到各个分片上，最终实现平衡


#### 3. 插入数据
- 当通过mongos添加数据，数据会被添加到预先分配好的chunk上，这样数据就不会在片之间来回移动产生大量的IO损耗



## foursqure 案例
- [foursqure](http://blog.nosqlfan.com/html/696.html)