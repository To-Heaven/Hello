# mapReduce


#### 介绍
- 其实从功能上来说，mapReduce就相当于传统型数据库的group分组操作

#### 应用/强项
- 海量数据分布式存储处理(shard)
	- 单台服务器的运算能力必然是有限的
	- mapReduce支持大量的服务器同时工作

#### mapReduce的工作过程
1. map映射
	- 把属于同一个group组的数据映射到一个数组上
2. reduce归约
	- 把map映射得到的数组内存放的数据进行运算


- 这里引用官方文档的图片

![https://docs.mongodb.com/manual/_images/map-reduce.bakedsvg.svg](mapreduce官方图)


## 使用mapReduce函数

#### 1. 定义map函数

- 使用map时我们需要自定义一个函数，该函数内要使用`emit(this.group_filed, this.arr_value)`函数
	- emit 用来生成数组
		- `group_field`是要进行分组的字段，每一个组都对应这一个数组
		- `arr_value`是每一个数据内要存放的值

#### 2. 定义reduce函数
- 定义reduce函数的时候，我们需要传递两个参数`key`, `values`
- 格式如下
	- method为JavaScript中数组的对应方法
	- **注意**
		- 为了我们数据处理方便，mongo给我们封装好了一些函数，使用for循环遍历Array我们可以得到这些函数

```
function (key, values){return Array.method(args)}

```


```
> for (var key in Array){ print(key);}
contains
unique
shuffle
tojson
fetchRefs
sum
avg
stdDev
```

###### sum
- 求和

###### unique


###### shuffle
- 打乱数据顺序

###### tojson
- 转换成json对象


###### avg
- 求平均值

###### contains


###### fetchRefs

###### stdDev



#### 3. 定义筛选语句
- 在mapReduce中使用js对象形式对结果进行 



#### 4. 指定存放集合
- 在mapReduce中使用js对象形式指出存放结果的collection
- 使用`out`关键字
	- `{query:{field: xxx}, out: "collectionName"}`



#### 举例

```
# 计算每一个商品栏目(cat_id)下的商品总量(good_number)

# mysql

select sum(good_number) from goods group by cat_id; 


# mongodb

var map = function(){
	emit(this.cat_id, this.good_number)	;
}


var reduce = function(cat_id, good_numbers){
	return Array.sum(good_numbers);
}

db.goods.mapReduce(map, reduce, {out: "result"})



# ------------------------------------------
# 计算每一个栏目下商品的平均价格

# mysql
select cat_id, avg(good_price) from goods group by cat_id;


# mongodb

var map = function(){
	emit(this.cat_id, this.good_price);
}


var reduce = function(cat_id, good_price){
	return Array.avg(good_price);
}

db.goods.mapReduce(map, reduce, {out: "acgprice"})

```

