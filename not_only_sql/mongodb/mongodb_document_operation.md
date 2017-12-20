## 文档操作
> mongodb中的文档是一个集合，键值对对应field和value

#### 插入数据
###### 插入单个数据
- `db.collectionName.insert({key1: value1, key2: value2})`
	- 被插入的对象可以是一个js对象，也可以是一个json对象，起始说白了，json对象本来就属于js对象。

- 注意
	- mongodb为每一个创建的文档默认添加主键`_id`，我们也可以自定义主键来覆盖默认值

```javascript
> db.userinfo.insert({name:"ziawang", age:18});
WriteResult({ "nInserted" : 1 })
> db.userinfo.insert({"name":"ziawang", "age":18});
WriteResult({ "nInserted" : 1 })
> db.userinfo.find();
{ "_id" : ObjectId("5a3931fc05fe0f70a5cb0937"), "name" : "ziawang", "age" : 18 }
{ "_id" : ObjectId("5a39325205fe0f70a5cb0938"), "name" : "ziawang", "age" : 18 }
> 
```

###### 插入多个数据
- 在JavaScript中，json是一个对象，因此可以将多个json对象放到一个数组里就可以一次性插入多个document
	- `db.colelectionName.insert([document1, document2[,...]])`

```javascript
> db.userinfo.insert([
... {"name": "zhangxin", "age": 15},
... {"name": "lixing", "age": 25, "gender": "female"},
... {"name": "bobo", "hobby": ["music", "food"]}
... ]);
BulkWriteResult({
        "writeErrors" : [ ],
        "writeConcernErrors" : [ ],
        "nInserted" : 3,
        "nUpserted" : 0,
        "nMatched" : 0,
        "nModified" : 0,
        "nRemoved" : 0,
        "upserted" : [ ]
})
> db.userinfo.find();
{ "_id" : ObjectId("5a3931fc05fe0f70a5cb0937"), "name" : "ziawang", "age" : 18 }
{ "_id" : ObjectId("5a39325205fe0f70a5cb0938"), "name" : "ziawang", "age" : 18 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093b"), "name" : "lixing", "age" : 25, "gender" : "female" }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo", "hobby" : [ "mus
```



#### 查看数据
###### 查看所有文档的所有（默认）
- `db.collectionName.find()`

###### 查看指定文档的指定列（即属性）
- `db.collectionName.find(查询表达式, 指定列)`
	- 参数指定列，用json对象的形式
		- mongodb中，value的值为非0代表true(包括负数)，value的值为0代表false。因此当指定的字段对应的值为非0的时候，就会显示该指定字段，否则就显示除该字段以外的其他字段
	- 当要取出所有的文档时，只需要在查询表达式的位置传入`{}`即可 

```javascript
> db.userinfo.find({}, {hobby:1});
{ "_id" : ObjectId("5a3931fc05fe0f70a5cb0937") }
{ "_id" : ObjectId("5a39325205fe0f70a5cb0938") }
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939") }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a") }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093b") }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "hobby" : [ "music", "food" ] }
> db.userinfo.find({}, {hobby:1, _id:0});
{  }
{  }
{  }
{  }
{  }
{ "hobby" : [ "music", "food" ] }
> 
```

- `_id`主键属性总是会被查询出来，如果早查询结果中不想要`_id`属性，可以将`_id`属性值设置为0
	- `db.colllection.find({name:'ziawang'}, { _id:0})`
		- 查询集合中所有满足查询表达式的文档中，满足name=ziawang，并且在结果中不显示_id`字段




###### 只查看一个
- 使用`db.collection.findOne()`
	- 在findOne中仍然可以使用查询表达式和指定显示字段

```javascript
> db.userinfo.findOne();
{
        "_id" : ObjectId("5a3931fc05fe0f70a5cb0937"),
        "name" : "ziawang",
        "age" : 18
}
> db.userinfo.findOne({name:"zhangxin"});
{
        "_id" : ObjectId("5a39331d05fe0f70a5cb0939"),
        "name" : "zhangxin",
        "age" : 15
}
> 
```


###### 查看collection中文档数量
- `db.collectionName.find().count()`

```javascript
> db.userinfo.count()
6
> db.userinfo.find({"name":"ziawang"}).count()
2
> 

```

#### 删除数据
###### 危险的删除

- `db.collectionName.remove(查询表达式)`
	- 查询表达式： 查询表达式类似于MySQL的where语句的作用
		- 查询表达式的表示形式是一个JSON对象，查询表达式会将匹配的文档删掉
		- `db.student.remove({name: 'gouzi'})`
			- 将student集合下的所有name属性值为gouzi的文档删除
	- **如果不传入查询表达式，就会删除collectionName下的所有文档**

- 注意
	- 不能使用`db.collectionName.find(查询表达式).remove()`这种语法是会**报错的**

```javascript
> db.userinfo.find();
{ "_id" : ObjectId("5a3931fc05fe0f70a5cb0937"), "name" : "ziawang", "age" : 18 }
{ "_id" : ObjectId("5a39325205fe0f70a5cb0938"), "name" : "ziawang", "age" : 18 }
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093b"), "name" : "lixing", "age" : 25, "gender" : "female" }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo", "hobby" : [ "music", "food" ] }
> db.userinfo.find({name:"lixing"}).remove();
2017-12-19T10:51:30.706-0500 E QUERY    [thread1] TypeError: db.userinfo.find(...).remove is not a function :
@(shell):1:1
> db.userinfo.remove({name:"lixing"});
WriteResult({ "nRemoved" : 1 })
> 
```




###### 较安全的删除
- `db.collectionName.remove(查询表达式, justOne=true/false) `
	- 当remove方法的第二个参数为true的时候，表示只删除查询表达式匹配到的一个对象
	- 如果为false，会删除匹配到的所有对象（默认false，因此true需要手动指定）

- 注意	
	- **justOne参数既可以以按位置传参，也可以按照关键字指定**

```javascript
> db.userinfo.remove({}, justOne=true);
WriteResult({ "nRemoved" : 1 })
> db.userinfo.find();
{ "_id" : ObjectId("5a39325205fe0f70a5cb0938"), "name" : "ziawang", "age" : 18 }
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo", "hobby" : [ "music", "food" ] }
> db.userinfo.remove({}, true);
WriteResult({ "nRemoved" : 1 })
> db.userinfo.find();
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo", "hobby" : [ "music", "food" ] }
> 
```


#### 修改文档
###### 修改文档所有内容（直接替换旧文档）
- `db.collectionName.update(查询表达式，新JSON文档)`

###### 赋值表达式修改文档指定内容（即指定列）
- 使用`$set`修改某一列的值
	- `db.collectionName.Update(查询表达式, {$set:JSON对象})`
		- `$set`后的json对象用来指定要修改的字段以及修改后的内容

```javascript
> db.userinfo.find();
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo", "hobby" : [ "music", "food" ] }
> db.userinfo.update({name:"zhangxin"}, {$set: {name: "xinxin"}});
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.userinfo.find();
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "xinxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo", "hobby" : [ "music", "food" ] }
> 
```

- 使用`$(unset)`删除指定文档中的某个列
	- `db.collectionName.update(查询表达式, { $unset: { <field1>: "", ... } })`
	- **指定某个列对应字段的值为空字符串即可**

```javascript
> db.userinfo.find();
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "xinxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo", "hobby" : [ "music", "food" ] }
> db.userinfo.update({name:"bobo"}, {$unset: {"hobby":""}});
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.userinfo.find();
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "xinxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo" }
> 
```

- 使用`$rename`重命名某个列
	- `db.collectionName.update(查询表达式, {$rename: { <field1>: <newName1>, <field2>: <newName2>, ... } })`
	- 字段的新名称用字符串形式表示

```javascript
> db.userinfo.find();
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "xinxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "name" : "zhangxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo" }
> db.userinfo.update({name:"zhangxin"}, {$rename: {name: "bigname"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.userinfo.find();
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "xinxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "age" : 15, "bigname" : "zhangxin" }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo" }
> 
```

- 使用`$inc`对匹配列增加指定值
	- `db.collectionName.update(查询表达式, { $inc: { <field1>: <amount1>, <field2>: <amount2>, ... }})` 

```javascript
> db.userinfo.find();
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "xinxin", "age" : 15 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "age" : 15, "bigname" : "zhangxin" }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo" }
> db.userinfo.update({name: "xinxin"}, {$inc: {"age":5}});
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.userinfo.find();
{ "_id" : ObjectId("5a39331d05fe0f70a5cb0939"), "name" : "xinxin", "age" : 20 }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093a"), "age" : 15, "bigname" : "zhangxin" }
{ "_id" : ObjectId("5a39334e05fe0f70a5cb093c"), "name" : "bobo" }
> 
``` 

###### 注意，update默认修改一个文档，即使匹配多条
- 当查询表达式匹配到了多个文档的时候，默认只修改一行，如果要修改多行的化，使用第三个可选参数`{multi:true}`。要注意这个multi参数的指定方式是使用类似JSON对象的格式
	- `db.collectionName.update(查询表达式, {$set:新json文档}, {multi:true})`
	
```javascript
> db.userinfo.insert([
... {"name": "ziawang", "age":20},
... {"name": "haha", "age":20},
... {"name": "ziawang", "age":18}
... ]);
BulkWriteResult({
        "writeErrors" : [ ],
        "writeConcernErrors" : [ ],
        "nInserted" : 3,
        "nUpserted" : 0,
        "nMatched" : 0,
        "nModified" : 0,
        "nRemoved" : 0,
        "upserted" : [ ]
})
> db.userinfo.update({age:20}, {$set:{"gender": "male"}});
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.userinfo.find();
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269ca"), "name" : "ziawang", "age" : 20, "gender" : "male" }
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269cb"), "name" : "haha", "age" : 20 }
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269cc"), "name" : "ziawang", "age" : 18 }
> db.userinfo.update({age:20}, {$set:{"gender": "female"}}, {multi:true});
WriteResult({ "nMatched" : 2, "nUpserted" : 0, "nModified" : 2 })
> db.userinfo.find();
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269ca"), "name" : "ziawang", "age" : 20, "gender" : "female" }
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269cb"), "name" : "haha", "age" : 20, "gender" : "female" }
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269cc"), "name" : "ziawang", "age" : 18 }
> 
```

- 关于`{$set: {xx:xx}}`的几点注意
	1. 如果修改一个不存在的字段，就会创建该字段


###### 修改一个不存在的文档，不会对集合由任何影响（也不会报错）

```javascript

> db.userinfo.update({"name":"unexist"}, {$set: {"age":20}});
WriteResult({ "nMatched" : 0, "nUpserted" : 0, "nModified" : 0 })
> 
```

###### $upsert修改一个不存在的文档，文档不存在则创建！
- 使用`$upsert`
	- `db.collectionName.update(查询表达式, {$set:新json文档}, {upsert:true})`

```javascript
> db.userinfo.update({"name":"unexist"}, {$set: {"age":20}});
WriteResult({ "nMatched" : 0, "nUpserted" : 0, "nModified" : 0 })
> db.userinfo.update({"name":"unexist"}, {$set: {"age":20}}, {upsert:true});
WriteResult({
        "nMatched" : 0,
        "nUpserted" : 1,
        "nModified" : 0,
        "_id" : ObjectId("5a3a4fc851aa66c621c70754")
})
> db.userinfo.find();
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269ca"), "name" : "ziawang", "age" : 20, "gender" : "female" }
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269cb"), "name" : "haha", "age" : 20, "gender" : "female" }
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269cc"), "name" : "ziawang", "age" : 18 }
{ "_id" : ObjectId("5a3a4fc851aa66c621c70754"), "name" : "unexist", "age" : 20 }
> 
```

###### $setOnInsert:{}
- 当参数存在`{upsert:true}`，由于文档不存在而发生insert操作时，存放用来附加补充到文档的字段
	- `db.collectionName.update(查询表达式, {$set:新json文档, {$setOnInsert:要补充的json文档}}, {upsert:true})`
	- 注意，`$setOnInsert`要放在`$set`中

```javascript
> db.userinfo.update({"unexist_field":"xxx"}, {$set: {"age":20}, $setOnInsert:{"name":"wangzihao"}}, {upsert:true});
WriteResult({
        "nMatched" : 0,
        "nUpserted" : 1,
        "nModified" : 0,
        "_id" : ObjectId("5a3a512751aa66c621c70761")
})
> db.userinfo.find();
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269ca"), "name" : "ziawang", "age" : 20, "gender" : "female" }
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269cb"), "name" : "haha", "age" : 20, "gender" : "female" }
{ "_id" : ObjectId("5a3a4e3f6ba4f479efb269cc"), "name" : "ziawang", "age" : 18 }
{ "_id" : ObjectId("5a3a4fc851aa66c621c70754"), "name" : "unexist", "age" : 20 }
{ "_id" : ObjectId("5a3a512751aa66c621c70761"), "unexist_field" : "xxx", "age" : 20, "name" : "wangzihao" }
> 
```

