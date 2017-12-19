## 集合操作
> 集合类似与mysql中的table



#### 创建集合

- mongodb的collections也支持隐式创建。直接向一个**尚未创建的collection中**插入文档会隐式的创建该collection，并向该collection中插入文档
	- `db.unexist_collection.insert(document)`
	
```
> use ziawang;
switched to db ziawang
> db.userinfo.insert({"name":"ziawang", "age": "23"});
WriteResult({ "nInserted" : 1 })
> show dbs;
Warning  0.000GB
ziawang  0.000GB
> db.userinfo.find();
{ "_id" : ObjectId("5a39313005fe0f70a5cb0936"), "name" : "ziawang", "age" : "23" }
> 
```

#### 查询集合
- `show tables` or `show collections`
	- 显示当前数据库下的所有集合

```
> show tables;
userinfo
> show collections;
userinfo
> 
```

#### 删除集合
- `db.collection_name.drop()`
	- 删除名称为`collection_name`的集合，删除成功后会返回一个`True`
	
```
> db.userinfo.drop();
true
> 
```