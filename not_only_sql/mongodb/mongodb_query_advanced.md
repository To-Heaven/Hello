## 查询表达式进阶
> mongodb中的所有查询表达式都是由JSON对象表示的

```
# 使用数据如下

> db.goods.find();
{ "_id" : 1, "name" : "a", "price" : 1, "category" : "fruit" }
{ "_id" : 2, "name" : "a", "price" : 2, "category" : "fruit" }
{ "_id" : 3, "name" : "c", "price" : 3, "category" : "fruit" }
{ "_id" : 4, "name" : "d", "price" : 5, "category" : "fruit" }
{ "_id" : 5, "name" : "t", "price" : 6, "category" : "fruit" }
{ "_id" : 6, "name" : "python", "price" : 4, "category" : "book" }
{ "_id" : 7, "name" : "linux", "price" : 3, "category" : "book" }
{ "_id" : 8, "name" : "golang", "price" : 8, "category" : "book" }
{ "_id" : 9, "name" : "dabaojian", "price" : 300, "category" : "other" }
{ "_id" : 10, "name" : "k", "price" : 30, "category" : "other" }
{_id:11, name:"dog", age:10}
```


###### $ne
- "不等于"， 等价于mysql中的`!=`
- `db.collectionName.find({field: {$ne:value}})`
	- 查询collectionName集合中，field值不等于value 的所有文档

```
# 查询种类不是fruit的商品
> db.goods.find({category: {$ne: "fruit"}});
{ "_id" : 6, "name" : "python", "price" : 4, "category" : "book" }
{ "_id" : 7, "name" : "linux", "price" : 3, "category" : "book" }
{ "_id" : 8, "name" : "golang", "price" : 8, "category" : "book" }
{ "_id" : 9, "name" : "dabaojian", "price" : 300, "category" : "other" }
{ "_id" : 10, "name" : "k", "price" : 30, "category" : "other" }

# 查询种类不是fruit的其他商品的名称和价格
> db.goods.find({category: {$ne:"fruit"}}, {name:1, price:1});
{ "_id" : 6, "name" : "python", "price" : 4 }
{ "_id" : 7, "name" : "linux", "price" : 3 }
{ "_id" : 8, "name" : "golang", "price" : 8 }
{ "_id" : 9, "name" : "dabaojian", "price" : 300 }
{ "_id" : 10, "name" : "k", "price" : 30 }



```

###### $gt
- "大于"， 等价于mysql中的`>`
- `db.collectionName.find({field:{$gt:value}})`
	- 查询collectionName集合中，field值大于value 的所有文档

```
# 查询价格大于5的商品
> db.goods.find({price: {$gt: 5}});
{ "_id" : 5, "name" : "t", "price" : 6, "category" : "fruit" }
{ "_id" : 8, "name" : "golang", "price" : 8, "category" : "book" }
{ "_id" : 9, "name" : "dabaojian", "price" : 300, "category" : "other" }
{ "_id" : 10, "name" : "k", "price" : 30, "category" : "other" }
```

###### $lt
- "小于"， 等价于mysql中的`<`
- `db.collectionName.find({field:{$lt:value}})`
	- 查询collectionName集合中，field值小于value的所有文档

```
# 查询价格小于5的商品

> db.goods.find({price: {$lt: 5}});
{ "_id" : 1, "name" : "a", "price" : 1, "category" : "fruit" }
{ "_id" : 2, "name" : "a", "price" : 2, "category" : "fruit" }
{ "_id" : 3, "name" : "c", "price" : 3, "category" : "fruit" }
{ "_id" : 6, "name" : "python", "price" : 4, "category" : "book" }
{ "_id" : 7, "name" : "linux", "price" : 3, "category" : "book" }
>
```



###### $gte
- "大于等于"
- `db.find`

###### $lte
- "小于等于"

###### $in
- "存在于"，等价于mysql中的`in (x, y)`
	- 常用于"或"的关系中
- `db.collectionName.find({field:{$in:[x, y, z]}})`


```
# 查询价格等于3或者5的商品名称


```

###### $nin
- "不存在于"，等价于mysql中的`not in`

```
# 查询种类既不是fruit也不是book，显示除了category之外的field
> db.goods.find({category: {$nin:["fruit", "book"]}});
{ "_id" : 9, "name" : "dabaojian", "price" : 300, "category" : "other" }
{ "_id" : 10, "name" : "k", "price" : 30, "category" : "other" }
>
```


###### $all:[x, y,z]
- 数组所有的元素都匹配上，才为真  

```
> db.info.insert({name:"ziawang", age:20, hobby:["study", "girls", "foods"]});
WriteResult({ "nInserted" : 1 })
> db.info.insert({name:"bobo", age:20, hobby:["girls", "foods", "swim"]});
WriteResult({ "nInserted" : 1 })
> db.info.insert({name:"beibei", age:"22", hobby:["girls"]});
WriteResult({ "nInserted" : 1 })
> db.info.find({age: {$type:2}});
{ "_id" : ObjectId("59f71f96522a565c21c0349f"), "name" : "beibei", "age" : "22", "hobby" : [ "girls" ] }
> db.info.find({hobby:{$all: ["girls"]}});
{ "_id" : ObjectId("59f71f5f522a565c21c0349d"), "name" : "ziawang", "age" : 20, "hobby" : [ "study", "girls", "foods" ] }
{ "_id" : ObjectId("59f71f70522a565c21c0349e"), "name" : "bobo", "age" : 20, "hobby" : [ "girls", "foods", "swim" ] }
{ "_id" : ObjectId("59f71f96522a565c21c0349f"), "name" : "beibei", "age" : "22", "hobby" : [ "girls" ] }
> db.info.find({hobby:{$all: ["girls","foods"]}});
{ "_id" : ObjectId("59f71f5f522a565c21c0349d"), "name" : "ziawang", "age" : 20, "hobby" : [ "study", "girls", "foods" ] }
{ "_id" : ObjectId("59f71f70522a565c21c0349e"), "name" : "bobo", "age" : 20, "hobby" : [ "girls", "foods", "swim" ] }
> db.info.find({hobby:{$all: ["girls","foods","stydy"]}});
> db.info.find({hobby:{$all: ["girls","foods","study"]}});
{ "_id" : ObjectId("59f71f5f522a565c21c0349d"), "name" : "ziawang", "age" : 20, "hobby" : [ "study", "girls", "foods" ] }
>
```


###### $or
- "或"




###### $and
- "与"
	- $and 逻辑运算内部可以有其他条件，当其他条件有多个的时候，要用数组封装在一起

```
# 查询种类既不是fruit也不是book，显示除了category之外的field
> db.goods.find({$and: [{category: {$ne:"fruit"}},{category:{$ne:"book"}}]}, {category:0});
{ "_id" : 9, "name" : "dabaojian", "price" : 300 }
{ "_id" : 10, "name" : "k", "price" : 30 }

```

###### $nor
- "既不是...也不是..."，跟英语中的`neigther ....nor ....`一样的意思
- `$nor`用起来和`$and`语法上差不多 

```
# 查询种类既不是fruit也不是book，显示除了category之外的field
> db.goods.find({$nor: [{category:"fruit"},{category:"book"}]});
{ "_id" : 9, "name" : "dabaojian", "price" : 300, "category" : "other" }
{ "_id" : 10, "name" : "k", "price" : 30, "category" : "other" }
>
```

###### $exists
- 某列存在则通过对应的值来确定是否返回
	- `{$exists: 1}` 表示当字段存在时为真，即显示包含该字段的文档
	- `{$exists: 0}` 表示当字段存在时为假，即显示不包含该字段的文档


```
#  查询
> db.goods.find({age: {$exists:1}});
{ "_id" : 11, "name" : "dog", "age" : 10 }
> db.goods.find({age: {$exists:0}});
{ "_id" : 1, "name" : "a", "price" : 1, "category" : "fruit" }
{ "_id" : 2, "name" : "a", "price" : 2, "category" : "fruit" }
{ "_id" : 3, "name" : "c", "price" : 3, "category" : "fruit" }
{ "_id" : 4, "name" : "d", "price" : 5, "category" : "fruit" }
{ "_id" : 5, "name" : "t", "price" : 6, "category" : "fruit" }
{ "_id" : 6, "name" : "python", "price" : 4, "category" : "book" }
{ "_id" : 7, "name" : "linux", "price" : 3, "category" : "book" }
{ "_id" : 8, "name" : "golang", "price" : 8, "category" : "book" }
{ "_id" : 9, "name" : "dabaojian", "price" : 300, "category" : "other" }
{ "_id" : 10, "name" : "k", "price" : 30, "category" : "other" }
>
```


###### $mod:[a, b]
- 满足求余条件则为真
	- 指定数值除以a的时候，如果余数刚好为b，则条件为真，返回该数据

```
#获取价格为2的倍数的商品
> db.goods.find({price: {$mod:[2,0]}});
{ "_id" : 2, "name" : "a", "price" : 2, "category" : "fruit" }
{ "_id" : 5, "name" : "t", "price" : 6, "category" : "fruit" }
{ "_id" : 6, "name" : "python", "price" : 4, "category" : "book" }
{ "_id" : 8, "name" : "golang", "price" : 8, "category" : "book" }
{ "_id" : 9, "name" : "dabaojian", "price" : 300, "category" : "other" }
{ "_id" : 10, "name" : "k", "price" : 30, "category" : "other" }
>
```

###### $type:num
- 数据为某类型则真
	- num为每一个类型规定的对应的数字
		- Double	1
		- String	2
		- Object	3
		- Array		4
		- Binary data  5
		- Udefined	6
		- Object id	7
		- Boolean	8
		- Date		9
		- Null		10




```
# 查询年龄的数据类型为字符串的文档
> db.info.insert({name:"ziawang", age:20, hobby:["study", "girls", "foods"]});
WriteResult({ "nInserted" : 1 })
> db.info.insert({name:"bobo", age:20, hobby:["girls", "foods", "swim"]});
WriteResult({ "nInserted" : 1 })
> db.info.insert({name:"beibei", age:"22", hobby:["girls"]});
WriteResult({ "nInserted" : 1 })
> db.info.find({age: {$type:2}});
{ "_id" : ObjectId("59f71f96522a565c21c0349f"), "name" : "beibei", "age" : "22", "hobby" : [ "girls" ] }
```


###### $where:str_expression
- js表达式为真则为真
	- 当在mongodb中执行查询语句的时候，会遍历磁盘上的BSON二进制对象（每一个文档都是一个BSON对象），并将BSON对象转换成JSON对象，因此可以用JavaScript中的判断语句来对文档对象的每一个属性进行判断


```
> db.goods.find({$where:"this.price>3"});
{ "_id" : 4, "name" : "d", "price" : 5, "category" : "fruit" }
{ "_id" : 5, "name" : "t", "price" : 6, "category" : "fruit" }
{ "_id" : 6, "name" : "python", "price" : 4, "category" : "book" }
{ "_id" : 8, "name" : "golang", "price" : 8, "category" : "book" }
{ "_id" : 9, "name" : "dabaojian", "price" : 300, "category" : "other" }
{ "_id" : 10, "name" : "k", "price" : 30, "category" : "other" }
> db.info.find({$where:"this.name=='ziawang'"});
{ "_id" : ObjectId("59f71f5f522a565c21c0349d"), "name" : "ziawang", "age" : 20, "hobby" : [ "study", "girls", "foods" ] }
>
```

- **不建议使用where，因为从BSON转换成JSON对象的过程会增加查询时间**


###### $regex
- 正则表达式匹配则为真
	- **效率也低**
	- 注意：
		- JavaScript中的正则表达式有两种表达方式

```
> db.info.find({name:{$regex: "^zia"}});
{ "_id" : ObjectId("59f71f5f522a565c21c0349d"), "name" : "ziawang", "age" : 20, "hobby" : [ "study", "girls", "foods" ] }
> db.info.find({name:{$regex: /^zia/}});
{ "_id" : ObjectId("59f71f5f522a565c21c0349d"), "name" : "ziawang", "age" : 20, "hobby" : [ "study", "girls", "foods" ] }
>
```


## 子文档查询
- 所谓子文档，即嵌套在文档中的文档
- 数据准备

```
> db.goods.insert({name: "banana", info:{price: "1.98", area: "hainan"}})
WriteResult({ "nInserted" : 1 })
> db.goods.insert({name: "apple", info:{price: "5.89", area: "fujian"}})
WriteResult({ "nInserted" : 1 })
> db.goods.insert({name: "zao", info:{price: "3.89", area: "ningxia"}})
WriteResult({ "nInserted" : 1 })
> db.goods.find();
{ "_id" : ObjectId("59f86865b9d6cba3de002b09"), "name" : "banana", "info" : { "price" : "1.98", "area" : "hainan" } }
{ "_id" : ObjectId("59f8687ab9d6cba3de002b0a"), "name" : "apple", "info" : { "price" : "5.89", "area" : "fujian" } }
{ "_id" : ObjectId("59f8688db9d6cba3de002b0b"), "name" : "zao", "info" : { "price" : "3.89", "area" : "ningxia" } }
>
```

#### 子查询
- `db.collectionName.find({str_parAttr.subAttr: value})`
	- 注意：锁定查询字段的语句`parAttr.subAttr`必须是字符串

```
> db.goods.find({info.area: "fujian"});
2017-10-31T20:18:36.885+0800 E QUERY    [thread1] SyntaxError: missing : after property id @(shell):1:19
> db.goods.find({"info.area": "fujian"});
{ "_id" : ObjectId("59f8687ab9d6cba3de002b0a"), "name" : "apple", "info" : { "price" : "5.89", "area" : "fujian" } }
>
```