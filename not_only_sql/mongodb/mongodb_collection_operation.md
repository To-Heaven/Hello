## 集合操作
> 集合类似与mysql中的table



#### 创建集合

- mongodb的collections也支持隐式创建。直接向一个**尚未创建的collection中**插入文档会隐式的创建该collection，并向该collection中插入文档
	- `db.unexist_collection.insert(document)`
	
```

```

#### 查询集合
- `show tables` or `show collections`
	- 显示当前数据库下的所有集合


#### 删除集合
- `db.collection_name.drop()`
	- 删除名称为`collection_name`的集合
	
