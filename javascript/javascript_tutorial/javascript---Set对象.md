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
- 无序，不能通过下标索引操作
- iterable可迭代的数据类型，可以用`for . . . of  `遍历


## Set对象的常用操作
- 去重
- 遍历

## Set对象的方法
- s.add(value)
- s.delete(value)

