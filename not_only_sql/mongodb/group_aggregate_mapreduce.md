## db.collection.group(document)

```
db.collection.group({
		key: {key1: 1, key2: 1},
		cond: {},
		reduce: function(curr, result){...}，
		initial: {},
		finalize: function(){...}
	})
```


#### document
- group函数按照document格式传入参数，参数如下
	- `key`: 分组的字段
	- `cond`: 查询条件，用于筛选数据，筛选出的数据在使用key参数进行分组，当要去除所有的时候，可以使用空`{}`
	- `reduce`： 聚合运算
		- curr: 代表每一个document，可以通过curr来操作文档中的数据
		- result:
	- `initial`: 初始化result中相关变量
	- `finalize`:  某一组内的所有document遍历完毕之后要执行的回调函数

#### mysql与mongodb

```
# mysql
select teacher, count(id) from stduent group by teacher where age > 10;

# mongodb

db.col.group({
		key: {teacher: 1},
		cond: {age: {$gt: 10}},
		reduce: function(curr, result){
			result.cnt += 1;
		},
		initial: {cnt: 0}
		
	})

```

- 在mysql中会自动生成一个字段名为`count(id)`的字段，而在mongodb中，我们需要自己指定一个名字作为字段名，在上面例子中，`cnt`就是我们指定的字段名，在集合中，并且使用`initial`将其初始化为0，集合中没出现一次teacher，cnt就会加一，相当一一个计数器，因此实现了和count相同的效果。即我们**需要自定义reduce的规则**


```
# 查询每一个栏目cat_id下最贵的商品数量good_price

# mysql
select cat_id, max(good_price) from goods group by cat_id;

# mongodb

db.col.group({
	key: {cat_id: 1}
	cond: {},
	reduce: function(curr, result){
		if (curr.good_price > result.max){
			result.max = curr.good_price;
		}
	}
	initial: {max: 0};
	})

```

- 其实可以将分组的过程看作一个遍历的过程，当商品按照cat_id分组之后，每一个cat_id下的商品会依次遍历，每一次遍历curr都指向当前这个document，result可以看作是该cat_id组内的一个对象，该对象的属性用来存放必要的值



```
# 查询每一个栏目下商品的平均价格

# mysql
select cat_id, avg(good_price) from goods group by cat_id;

# mongodb

db.col.group({
	key: {cat_id: 1},
	conf: {},
	reduce: function(curr, result){
		result.cnt += 1；
		result.sum += curr.good_price;
	},
	initial: {sum: 0, cnt: 0},
	finalize: function(result){
		result.avg = result.sum/result.cnt;
	},
})
```


#### 注意
- group需要我们手写聚合函数的业务逻辑

- **group不支持分shard片和集群cluster**，无法分布式运算
- 想要实现分布式，我们可以使用aggregate(version2.4), mapReduce(version2.4)