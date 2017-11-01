## 索引
- 在MySQL中，由于默认使用InnoDB引擎，MySQL使用的是btree组织索引
- 在mongodb中默认也是使用btree组织索引文件，目前也已经支持hash索引


- 准备数据

```
> for (var i=1; i<1000; i++){
... db.customer.insert({
... cn:i,
... name:`customer ${i}`
... })};
WriteResult({ "nInserted" : 1 })
> db.customer.find().count();
999
>
```

#### mongodb查询数据的过程
- 在没有索引的情况下，mongodb会从头开始遍历，直到找到满足条件的数据


#### 索引的优缺点
- 优点
	- 索引可以提高查询效率

- 缺点
	- 索引会降低数据写入速度，因为写入一个文档不光要将文档插入到集合，还要创建与之相关的索引。





## 索引操作(btree索引)

#### 创建普通索引
###### 创建单列索引
- `db.collectionName.ensureIndex({field:1/-1})`
	- `field`是要被创建的索引字段
	- `1`按照升序排列建立索引
	- `-1`按照降序排列建立索引
	- 升序与降序的区别？
		- 待补充
	- 注意参数传递的形式

###### 创建多列索引
- `db.collectionName.ensureIndex({field1:1/-1, field2:1/-1})`
	- 注意，这是将多个列绑定成一个整体再建立成索引




###### 创建子文档索引
- `db,collectionName.ensureIndex({str_parAttr.subAttr: 1/-1})`


```

```


#### 创建唯一索引 {unique:true}
- 唯一索引即创建索引的字段值在集合的所有文档中是唯一的
- 如果插入的文档中的字段包含已经存在的字段值，就会报错


#### 创建稀疏索引	 {sparse:true}
- mongodb与MySQL不同的是它具有灵活的文档结构，因此我们在给集合创建索引的时候，可能有的字段根本就不存在我们创建的索引字段
	- 如果使用"普通索引"，对于文档中不存在索引字段的，mongodb会把该文档的该字段值认为NULL（可以通过查询字段值为NULL获取）
	- 如果我们使用"稀疏索引"，对于文档中不存在的索引字段，mongodb将不会对该文档建立索引，这样更节省资源，适用于小部分文档含有某列时



#### 删除索引
###### 删除指定索引
- `db.collectionName.dropIndex({field:1/-1})`

###### 删除所有索引
- ``


#### 查看索引
- `db.collectionName.getIndexes()`
	- 注意index是复数形式

## hash索引
- hash索引是利用hash算法对document对象对象计算，得到一个地址，然后该文档对象就存放在硬盘中对应的地址。这意味这使用hash算法建立的索引会让文档散列在硬盘中分布，当我们要查询某一个对象的时候，会先通过hash算法对对象进行计算，然后取计算的到的结果中取获取文档对象。因此，hash索引的时间复杂度在理论上是O(1)
- 当对某一个范围进行查询时，hash索引的效率可能不如btree索引的效率
	- 对于普通机械硬盘来说，顺序查找要比乱序查找更方便，btree索引效率更高。在内存中hash索引会很快

#### 创建hash索引  {field: "hashed"}
- `db.collectionName.ensureIndex({field: "hashed"})`


#### 重建索引
- 一张表(集合)经过多次修改之后，表中的文件会产生空洞，索引文件也是如此，可以通过索引的重建来提高索引的效率，达到减少索引碎片的目的。类似MySQL中的`optimize table`

- `db.collectionName.reIndex()`

