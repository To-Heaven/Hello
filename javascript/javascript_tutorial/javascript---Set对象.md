# Set对象

## Set对象介绍
- Set对象由一对大括号和一个个不重复的值构成
- Set对象中存储的是一个个没有重复的值。Set对象中存放的值可以是任意类型

## 获取Set对象
- 实例化Set类

```javascript
var set_ziawang = new Set(['wangzihao', 20, 'non-marriage', 'has girlfriend'])     // 初始化一个Set对象 
console.log(set_ziawang)			// {"wangzihao", 20, "non-marriage", "has girlfriend"}
// 创建一个空Set对象
var set_ziawang = new Set()

set_ziawang.add('wangzihao')
set_ziawang.add(20)
set_ziawang.add('non-marriage')
console.log(set_ziawang)		// {"wangzihao", 20, "non-marriage"}

set_ziawang.delete('non-marriage')
console.log(set_ziawang)		// {"wangzihao", 20}

```


## Set对象的特点
- 值不能重复
- **无序**，不能通过下标索引操作
- iterable可迭代的数据类型，可以用`for . . . of  `遍历


## Set对象的常用操作
- 去重

```javascript
var person = new Set(["name", "name",  "age", "gender", "city", "hobby", "height", "weight"]);

console.log(person)		// {"name", "age", "gender", "city", "hobby", "height", "weight"}
```

- 遍历

```javascript
for (let ele of person){console.log(ele)};
// -----------输出结果----------
name
age
gender
city
hobby
height
weight

```


## Set对象的属性
- s.size
	- 返回集合的元素个数



## Set对象的方法
#### 增

- s.add(value)
	- 向集合中添加新的值

#### 删除

- s.delete(value)
	- 从集合中删除value，删除成功则返回true，否则就返回false

```javascript
person.delete("city")			// true
person.delete("city")			// flase
```


#### 查
- s.has(value)
	- 如果value存在，就返回true
	- 否则就返回false


