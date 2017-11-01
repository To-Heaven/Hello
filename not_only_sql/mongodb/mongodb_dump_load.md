## 备份与还原
## 通用选项
- mongodb的导入导出既可以是在本地服务器也可以是远程的主机，因此有以下通用选项
	1. `-h`					host  主机
	2. `-port` 				port  端口
	3. `-u` 				user  用户
	4. `-p`					pwd	  密码  

## 数据库的导出
- 选择
	- 如果是为了与其他类型服务器(mysql等)进行数据交换，应该使用mongoexport导出json结构
	- 如果是为了备份，并且备份数据库的结构信息以防意外，应使用mongodump导出bson结构

#### 使用mongodump导出二进制bson结构及其索引信息
###### 参数
- `d`
	- 导出的数据库名
- `c`
	- 导出的集合名

- 注意
	- 使用`mongodump`导出对象之后，会在指定目录（默认当前目录）生成一个dump文件夹，该文件夹下的子文件夹存放了导出的文件，导出命令及目录结构如下

```
C:\Users\ziawa>mongodump -d mydb -c mycollection
2017-11-01T20:13:18.049+0800    writing mydb.mycollection to
2017-11-01T20:13:18.229+0800    done dumping mydb.mycollection (10000 documents)
---------------------目录结构------------------------
dump----|
		|--mydb
			|
			|--mycollection.bson				// bson结构的数据文件
			|--mycollection.metadata.json		// json结构的索引文件

```


#### 使用mongoexport导出数据库
###### 参数
- `-d`
	- 要导出的数据库
- `-c` 
	- 到导出的集合
- `-f`
	- 要导出的列，用逗号隔开
- `-q`
	- 查询条件，用引号包含起来
		- 注意
			- 在Windows的话使用双引号
- `-o` 
	- 指定导出文件及路径
	
- `--csv`
	- 导出csv格式 ，便于和传统数据库交换数据




```
#  导出json文件
C:\Users\ziawa>mongoexport -d mydb -c mycollection -f book,price -q "{price:{$lt:1000}}" -o mydb.mycollection.txt
2017-11-01T19:27:14.198+0800    connected to: localhost
2017-11-01T19:27:14.274+0800    exported 1000 records

#  导出csv文件
C:\Users\ziawa>mongoexport -d mydb -c mycollection -f book,price --type=csv -o my.csv
2017-11-01T19:51:32.934+0800    connected to: localhost
2017-11-01T19:51:33.116+0800    exported 10000 records

```


## 数据库的导入
#### mongorestore 导入二进制
###### 参数
- `d`
	- 用来接收导入数据的数据库
- `--directoryperdb`
	- mongodump导出数据时备份的目录

#### 使用mongoimport 导入到数据库
###### 参数
- `-d` 接收导入数据的数据库
- `-c` 数据库中接收导入数据的集合
- `-f` 指定要导入列
- `--type` 本分文件的类型，默认json，可以指定为csv
	- 导入csv文件的时候需要指定导入到数据库时候字段的新名称
- `--file` 备份文件的路径

```dos
# 导入json文件

C:\Users\ziawa>mongoimport -d test -c book --type json --file mydb.json
2017-11-01T19:45:09.499+0800    connected to: localhost
2017-11-01T19:45:09.687+0800    imported 1000 documents

# 导入csv文件
C:\Users\ziawa>mongoimport -d test -c bookcsv -f book,price  --type csv --file my.csv
2017-11-01T19:54:13.254+0800    connected to: localhost
2017-11-01T19:54:13.669+0800    imported 10001 documents

C:\Users\ziawa>
```

```
# 结果如下
> show tables;
book
bookcsv
first
> db.book.find().limit(10);
{ "_id" : ObjectId("59f9ab561450ae7051f87996"), "book" : "book1", "price" : 1 }
{ "_id" : ObjectId("59f9ab561450ae7051f87995"), "book" : "book0", "price" : 0 }
{ "_id" : ObjectId("59f9ab561450ae7051f87998"), "book" : "book3", "price" : 3 }
{ "_id" : ObjectId("59f9ab561450ae7051f8799a"), "book" : "book5", "price" : 5 }
{ "_id" : ObjectId("59f9ab561450ae7051f87999"), "book" : "book4", "price" : 4 }
{ "_id" : ObjectId("59f9ab561450ae7051f8799b"), "book" : "book6", "price" : 6 }
{ "_id" : ObjectId("59f9ab561450ae7051f8799d"), "book" : "book8", "price" : 8 }
{ "_id" : ObjectId("59f9ab561450ae7051f8799c"), "book" : "book7", "price" : 7 }
{ "_id" : ObjectId("59f9ab561450ae7051f87997"), "book" : "book2", "price" : 2 }
{ "_id" : ObjectId("59f9ab561450ae7051f8799e"), "book" : "book9", "price" : 9 }
> db.bookcsv.find().limit(10);
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3b5"), "book" : "book", "price" : "price" }
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3b6"), "book" : "book3", "price" : 3 }
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3b7"), "book" : "book4", "price" : 4 }
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3b8"), "book" : "book2", "price" : 2 }
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3b9"), "book" : "book5", "price" : 5 }
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3ba"), "book" : "book6", "price" : 6 }
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3bb"), "book" : "book7", "price" : 7 }
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3bc"), "book" : "book0", "price" : 0 }
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3bd"), "book" : "book1", "price" : 1 }
{ "_id" : ObjectId("59f9b5e52d05dfdf9e89e3be"), "book" : "book11", "price" : 11 }

```

###### csv文件导入的坑
- 从上面可以看见，将csv文件导入到数据库中的时候，mongo会将字段名也当作一个文档导入，因此我们需要在导入的时候，跳过第一行
- `--headerline`

```

```

#### 
