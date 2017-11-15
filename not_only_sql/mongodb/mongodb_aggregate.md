# aggregate聚集框架

## 使用aggregate

- aggregate的参数同样是用JavaScript对象表示，每一个key都代表一种聚集函数，常用的聚集函数有
	- `$group`， 对应MySQL中的`where`和`group by`（与位置有关）
	- `$match`，对应MySQL中的`having`
		- match对应的value是一个查询表达式
	- `$project`，对应MySQL中的`select`
	- `$sort`，对应MySQL中的`sort`
		- 在mongo中，1代表升序排列，-1代表降序排列
	- `$limit`，对应MySQL中的`limit`
		- limit对应的值为一个数字，表示要返回的数量
	- `$sum`，对应MySQL中的`sum()`
	- `$sum`，对应MySQL中的`count()`
	- `$avg`，对应MySQL中的`avg()`
	- `$min`
	- `$max`
	- `$first`
	- `$last`
	- `$push`
	- `$addToSet`

#### 语法

- 当JavaScript参数对象只有一个的时候，直接将该对象按位置传参即可 

```
db.collectionName.aggregate({key: {...}})
```


- 当JavaScript参数对象有多个的时候，我们需要将这些对象先存在一个数组集合中，在传入aggregate中

```
db.collectionName.aggregate(
		[
			{$match: {price: {$gt: 10}}},
			{$group: {_id: "$id"}}
		]
	)
```

#### 一定要注意
- 如果要在aggregate中使用文档中的字段，字段名的格式应该是这样子`"$field_name"`

## 分组 {$group: {_id: '$key_name'}}
- `_id`是一个固定的字段，其对应value中的`key_name`就是集合中要被分组的字段 

```
# 查询每个栏目(cat_id)下的商品数量

# mysql
select cat_id, count(*) from goods group by cat_id

# mongodb
db.good.aggregate({$group: {_id: "$cat_id", total: {$sum: 1}}})

```

## {$sum: "$filed_name"}

```

# 查询goods表下的所有商品数量

# mysql
select count(*) from goods

# mongodb
db.goods.aggregate({$group: {_id: null, total: {$sum: 1}}})

# ----------------------------------------

# 查询每一个栏目下价格(good_price)大于50元的商品个数

# mysql
select cat_id, count(*) from goods where good_price>50 group by cat_id 

# mongodb

db.goods.aggregate(
		[
			{$match: {good_price: {$gt: 50}}},
			{$group: {_id: "$cat_id", total: {$sum: 1}}}
		]
	)


# ----------------------------------------------------
# 查询每一个栏目下价格(good_price)大于50元的商品个数，并且只显示满足条件的商品个数大于等于3的栏目

# mysql
select cat_id, count(*) from goods where good_price > 50 group by cat_id having count(id)>=3;

# mongodb

db.goods.aggregate(
		[
			{$match: {$gt: 50}},
			{$group: {_id: "$cat_id", total: {$sum: 1}}},
			{$match: {total: {$gte: 3}}}
		]
	)
# --------------------------------------------------------

# 查询每个栏目下的库存量(goods_number)


# mysql
select cat_id, sum(goods_number) from goods group by cat_id

# mongodb
db.goods.aggregate(
		{$group: {_id: "$cat_id", total: {$sum: "$goods_number"}}}
	)

```

## {$sort: 1/-1}


```
# 查询每个栏目下的库存量(goods_number)，并按照库存量来排序

# mysql
select cat_id, sum(goods_number) as total from goods group by cat_id order by total;


# mongodb
db.goods.aggregate(
		[
			{$group: {_id: "$cat_id", total: {$sum: "$goods_number"}}},
			{$sort: {total: 1}}
		]
	)



# 查询每个栏目下的库存量(goods_number)，取出库存量最多的三条

# mysql
# select cat_id, sum(goods_number) as total from goods group by cat_id order by total desc limit 3;


# mongodb
db.goods.aggregate(
		[
			{$group: {_id: "$cat_id", total: {$sum: "$goods_number"}}},
			{$sort: {total: -1}},
			{$limit: 3}
		]
	)


```

## {$avg : "$field_name"}

```

# 查询每一个栏目商品的平均价格。并按照平均价格从高到低排序

# mysql

select cat_id, avg(good_price) as avg_price from goods group by cat_id order by avg_price desc


# mongodb

db.goods.aggregate(
		[
			{$group: {_id: "$cat_id", avg_price: {$avg: "$good_price"}}},
			{$sort: -1}	
		]
	)

```


## $match
#### $match放在$group之前
- 此时`$match`对应的查询表达式起到的作用就像MySQL中的where，因为此时还没有执行到`$group`进行分组，这个时候`$match`操作的将是整个collection中的所有文档



#### $match放在$group之后
- 此时集合已经经过了`$group`的分组，因此`$match`操作的是分组之后的结果，可以将其看作MySQL中的having过滤关键字