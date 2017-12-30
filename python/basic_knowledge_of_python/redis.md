# python 实现与 redis 服务端交互


## 链接

#### 连接数据库
- 使用python连接redis服务器有多种方式，但是推荐使用`链接池`的方式
	
###### 1. 直接建立连接
- 直接实例化一个`Redis`对象

```python
import redis

conn = redis.Redis(
    host='172.96.203.6',
    port=6379,
    password='passredis',
    charset='utf-8'
)


```


###### 2. 使用连接池

```python
conn_pool = redis.ConnectionPool(
    host='172.96.203.6',
    port=6379,
    password='passredis',
)

conn = redis.Redis(connection_pool=conn_pool)

```

## 数据操作
#### 字符串
- 插入字符串

```python
conn.set(key, value)
```

- 获取字符串值

```python
conn.get(key)
```


#### 列表
###### 插入值
- 从列表左侧插入单个值
	- `conn.lpush(key, value)`
- 将一个列表中的值按照顺序插入到另一个列表左侧
	- `conn.lpush(key, *args)`（其实是把arg序列中的值一个个的append到rediskey对应的列表中）

- 从列表右侧插入单个值
	- `conn.rpush(key, value)`

- 将列表中的值按照顺序插入到另一个列表的右侧
	- `conn.rpush(key, *args)`


###### 获取值
- 查询列表指定索引位置的值
	- `conn.lindex(key, num)`

- 取出列表最左侧的值
	- `conn.lpop(key)`

- 取出列表最右侧的值
	- `conn.rpop(key)`

###### 其他
- 获取存储的列表长度
	- `conn.llen(key)`


#### 公共操作
###### 删除一组键值对
- `conn.delete(key)`