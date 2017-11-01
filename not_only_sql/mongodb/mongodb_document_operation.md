## 文档操作
> mongodb中的文档是一个集合，键值对对应field和value

#### 插入数据
###### 插入单个数据
- `db.collectionName.insert({key1: value1, key2: value2})`

- 注意
	- mongodb为每一个创建的文档默认添加主键`_id`，我们也可以自定义主键来覆盖默认值

```

```

###### 插入多个数据
- 在JavaScript中，json是一个对象，因此可以将多个json对象放到一个数组里就可以一次性床i教案多篇document
	- `db.colelectionName.insert([document1, document2[,...]])`

```sql

```



#### 查看数据
###### 查看所有文档的所有（默认）
- `db.collectionName.find()`

###### 查看指定文档的指定列（即属性）
- `db.collectionName.find(查询表达式, 指定列)`
	- 参数指定列，用json对象的形式
		- mongodb中，value的值为非0代表true(包括负数)，value的值为0代表false。因此当指定的字段对应的值为非0的时候，就会显示该指定字段，否则就显示除该字段以外的其他字段

```

```

- `_id`主键属性总是会被查询出来，如果早查询结果中不想要`_id`属性，可以将`_id`属性值设置为0
	- `db.colllection.find(查询表达式, {name:'ziawang', _id:0})`
		- 查询集合中所有满足查询表达式的文档中，满足name=ziawang，并且在结果中不显示_id`字段




###### 只查看一个



###### 查看collection中文档数量
- `db.collectionName.find().count()`

#### 删除数据
###### 危险的删除

- `db.collectionName.remove(查询表达式)`
	- 查询表达式： 查询表达式类似于MySQL的where语句的作用
		- 查询表达式的表示形式是一个JSON对象，查询表达式会将匹配的文档删掉
		- `db.student.remove({name: 'gouzi'})`
			- 将student集合下的所有name属性值为gouzi的文档删除
	- **如果不传入查询表达式，就会删除collectionName下的所有文档**

```sql

```

###### 较安全的删除
- `db.collectionName.remove(查询表达式, justOne=true/false) `
	- 当remove方法的第二个参数为true的时候，表示只删除查询表达式匹配到的一个对象
	- 如果为false，会删除匹配到的所有对象（默认false，因此true需要手动指定）

```sql

```


#### 修改文档
###### 修改文档所有内容（直接替换旧文档）
- `db.collectionName.update(查询表达式，新JSON文档)`

###### 赋值表达式修改文档指定内容（即指定列）
- 使用`$set`修改某一列的值
	- `db.collectionName.Update(查询表达式, {$set:新JSON文档})`

- 使用`$(unset)`删除某个列
	- `db`

- 使用`$rename`重命名某个列


- 使用`$inc`对匹配列增加指定值
	
```sql

``` 

###### 注意，update默认修改一个文档，即使匹配多条
- 当查询表达式匹配到了多个文档的时候，默认只修改一行，如果要修改多行的化，使用第三个可选参数`{multi:true}`。要注意这个multi参数的指定方式是使用类似JSON对象的格式
	- `db.collectionName.update(查询表达式, {$set:新json文档}, {multi:true})`
	
```

```

###### 修改一个不存在的文档，不会对集合由任何影响（也不会报错）


###### 修改一个不存在的文档，文档不存在则创建！
- 使用`$upsert`
	- `db.collectionName.update(查询表达式, {$set:新json文档}, {upsert:true})`

```
```

###### $setOnInsert:{}
- 当参数存在`{upsert:true}`，由于文档不存在而发生insert操作时，存放用来附加补充到文档的字段
	- `db.collectionName.update(查询表达式, {$set:新json文档, {$setOnInsert:要补充的json文档}}, {upsert:true})`

```
```

