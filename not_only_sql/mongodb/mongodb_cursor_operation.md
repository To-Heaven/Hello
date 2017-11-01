## 在mongodb中使用JavaScript语法
- mongodb的解释器是由JavaScript实现的，在交互式控制台上我们除了做简单的逻辑运算和数学运算之外，甚至可以使用其他语法，比如`for`循环。
- 这里使用`for`来向一个集合中插入1000条数据

```
> for (var i=0;i<1001; i++){
... db.test.insert({_id:i, content:`this is comtent ${i} `})		// 你妹看错，这里甚至可以使用JavaScript的模板字符串
... };
WriteResult({ "nInserted" : 1 })		
> db.test.find().count();
1001
> db.test.find()
{ "_id" : 0, "content" : "this is comtent 0 " }
{ "_id" : 1, "content" : "this is comtent 1 " }
{ "_id" : 2, "content" : "this is comtent 2 " }
{ "_id" : 3, "content" : "this is comtent 3 " }
{ "_id" : 4, "content" : "this is comtent 4 " }
{ "_id" : 5, "content" : "this is comtent 5 " }
{ "_id" : 6, "content" : "this is comtent 6 " }
{ "_id" : 7, "content" : "this is comtent 7 " }
{ "_id" : 8, "content" : "this is comtent 8 " }
{ "_id" : 9, "content" : "this is comtent 9 " }
{ "_id" : 10, "content" : "this is comtent 10 " }
{ "_id" : 11, "content" : "this is comtent 11 " }
{ "_id" : 12, "content" : "this is comtent 12 " }
{ "_id" : 13, "content" : "this is comtent 13 " }
{ "_id" : 14, "content" : "this is comtent 14 " }
{ "_id" : 15, "content" : "this is comtent 15 " }
{ "_id" : 16, "content" : "this is comtent 16 " }
{ "_id" : 17, "content" : "this is comtent 17 " }
{ "_id" : 18, "content" : "this is comtent 18 " }
{ "_id" : 19, "content" : "this is comtent 19 " }
Type "it" for more
> it
```

- 那么问题来了，当我们要查询的时候，不可能想要一下子显示出全部的数据，也许我们向只显示最后一百行，后者中间一百行，这个时候，就需要用到"游标"


## 游标
#### 介绍
- 简单说，游标就是用来查询的返回资源或接口，通过这个接口我们可以逐条读取文档。
	- 它有点类似python中的句柄`f = open("filename")`

#### 创建游标
- 创建一个游标的语法和JavaScript中语法很像
	- `var cursor = db.collectionName.find()`
		- `db.collectionName.find()`的结果返回给了创建的游标变量。通过游标来按照我们想要的方式来获取数据

#### 特点
###### 

######

######


##游标的方法
#### 基本用法
###### Cursor.hasNext()
- 判断游标所在位置之后是否还有数据，即判断游标是否已经走到了尽头


###### Cursor.next()
- 返回游标的所在位置下一个元素
- 当游标没有下一个值的时候，再次调用cursor.next()会出错

```
> var cursor = db.test.find({_id:{$lt:10}});
> print(cursor.next());
[object BSON]
> printjson(cursor.next());
{ "_id" : 1, "content" : "this is comtent 1 " }
> printjson(cursor.next());
{ "_id" : 2, "content" : "this is comtent 2 " }
> printjson(cursor.next());
{ "_id" : 3, "content" : "this is comtent 3 " }
> printjson(cursor.next());
{ "_id" : 4, "content" : "this is comtent 4 " }
> printjson(cursor.next());
{ "_id" : 5, "content" : "this is comtent 5 " }
> printjson(cursor.next());
{ "_id" : 6, "content" : "this is comtent 6 " }
> printjson(cursor.next());
{ "_id" : 7, "content" : "this is comtent 7 " }
> printjson(cursor.next());
{ "_id" : 8, "content" : "this is comtent 8 " }
> printjson(cursor.next());
{ "_id" : 9, "content" : "this is comtent 9 " }
> printjson(cursor.next());
2017-10-31T19:05:32.798+0800 E QUERY    [thread1] Error: error hasNext: false :
DBQuery.prototype.next@src/mongo/shell/query.js:305:1
@(shell)
```

- print(obj)
	- 打印对象
- printjson(BSON_obj)
	- 打印对象的json对象格式




- 也可以配合`while`来打印输出


```
> var cursor = db.test.find({_id: {$lt: 5}});
> while (cursor.hasNext()){
... printjson(cursor.next())
... }
{ "_id" : 0, "content" : "this is comtent 0 " }
{ "_id" : 1, "content" : "this is comtent 1 " }
{ "_id" : 2, "content" : "this is comtent 2 " }
{ "_id" : 3, "content" : "this is comtent 3 " }
{ "_id" : 4, "content" : "this is comtent 4 " }
```


- 配合`for`打印输出

```
> for (var cursor=db.test.find({_id: {$lt:5}}); cursor.hasNext();){
... printjson(cursor.next())
... }
{ "_id" : 0, "content" : "this is comtent 0 " }
{ "_id" : 1, "content" : "this is comtent 1 " }
{ "_id" : 2, "content" : "this is comtent 2 " }
{ "_id" : 3, "content" : "this is comtent 3 " }
{ "_id" : 4, "content" : "this is comtent 4 " }
>
```

#### 遍历
###### forEach(callback_func)
- 游标的迭代函数，可以自定义回调函数来逐个处理每一个单元
	- 回调函数的参数就是每一个BSON格式的文档对象

```
> var cursor = db.test.find({_id: {$lt: 5}});
> cursor.forEach(function(obj){
... print("your id is " + obj._id)			// 文档是json对象，自然可以使用句点符调用其属性
... });
your id is 0
your id is 1
your id is 2
your id is 3
your id is 4
>
```


#### 分页
###### skip(row_num) 和 limit()
- skip(row_num)
	- 从当前游标中跳过指定数量的document

- limit(row_num)
	- 限制游标往下读取的文档数目直至到达集合末尾

```
> var cursor = db.test.find().skip(995);
> cursor.forEach(function(obj)
... {printjson(obj)
... });
{ "_id" : 995, "content" : "this is comtent 995 " }
{ "_id" : 996, "content" : "this is comtent 996 " }
{ "_id" : 997, "content" : "this is comtent 997 " }
{ "_id" : 998, "content" : "this is comtent 998 " }
{ "_id" : 999, "content" : "this is comtent 999 " }
{ "_id" : 1000, "content" : "this is comtent 1000 " }

> var cursor = db.test.find().skip(995).limit(10);

> cursor.forEach(function(obj){
... printjson(obj)
... });
{ "_id" : 995, "content" : "this is comtent 995 " }
{ "_id" : 996, "content" : "this is comtent 996 " }
{ "_id" : 997, "content" : "this is comtent 997 " }
{ "_id" : 998, "content" : "this is comtent 998 " }
{ "_id" : 999, "content" : "this is comtent 999 " }
{ "_id" : 1000, "content" : "this is comtent 1000 " }		// 到达末尾。只显示5条

> cursor.forEach(function(obj){
... printjson(obj)
... });
{ "_id" : 0, "content" : "this is comtent 0 " }
{ "_id" : 1, "content" : "this is comtent 1 " }
{ "_id" : 2, "content" : "this is comtent 2 " }
{ "_id" : 3, "content" : "this is comtent 3 " }
{ "_id" : 4, "content" : "this is comtent 4 " }


```


- 甚至可以不用cursor，直接使用`skip()`和`limit()`先获取指定的文档

```
> db.test.find().skip(990).limit(5);
{ "_id" : 990, "content" : "this is comtent 990 " }
{ "_id" : 991, "content" : "this is comtent 991 " }
{ "_id" : 992, "content" : "this is comtent 992 " }
{ "_id" : 993, "content" : "this is comtent 993 " }
{ "_id" : 994, "content" : "this is comtent 994 " }
> var cursor = db.test.find().skip(995);
```


###### toArray()
- 将有表中的文档一次性存放在数组中返回。
- 我们可以对返回值使用索引来获取指定值
- 不建议使用
	- 他会把cursor中的所有的行立即变成对象保存在内存中

```
> var arr_document = cursor.toArray();
> printjson(arr_document);
[
        {
                "_id" : 995,
                "content" : "this is comtent 995 "
        },
        {
                "_id" : 996,
                "content" : "this is comtent 996 "
        },
        {
                "_id" : 997,
                "content" : "this is comtent 997 "
        },
        {
                "_id" : 998,
                "content" : "this is comtent 998 "
        },
        {
                "_id" : 999,
                "content" : "this is comtent 999 "
        },
        {
                "_id" : 1000,
                "content" : "this is comtent 1000 "
        }
]
>
```

- 因此，document虽然是二进制形式的json对象，我们仍然可以使用JavaScript的思路来操作mongodb中的document



 


