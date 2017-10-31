# Map（映射）对象

## Map对象介绍
- 类似其他语言中的字典数据类型，在JavaScript中，Map对象用于存放键值对。
- JavaScript的Map对象键值对中的key 和 value 可以是任意类型的数据，甚至是undefined
- Map对象的结构

```javascript

{key1: value1, key2: value2, ...}

```


## 获取Map对象
- 实例化Map
	- Map()参数为嵌套的数组

```javascript
map_empty = new Map();				// 获取空Map对象
map_empty.set('name', 'ziawang');
map_empty.get('name')

// ---------------------------------
arr_kv = [['name', 'ziawang'], ['age', 10], ['hasGirl', true]]; 
map_obj = new Map(arr_kv);

```



## Map对象的特点
- key和value可以是任意的JavaScript数据类型
- 无序，没有下标索引，因此不通过索引来操作Map对象
- 可迭代的数据类型，可以使用` for  ... of ...` 遍历
- 查询效率高


## Map对象要注意的地方
- 与对象不同，对象虽然也是大括号中的一个个键值对，但是对象的key最终都是字符串，而Map对象中的键可以是任意类型！
- 多次向同一个Map对象中插入相同key的键值对，js只会记住最后插入的key-value
- 不能用"句点符"`.`和中括号`m["key"]`的形式来访问Map中key对应的value 

## Map的常用操作
- 使用中括号查看value

```javascript
var info_ziawang = new Map();
info_ziawang.set("name", "ziawang");

var name = m.get("name");								// ziawang
info_ziawang.get("name") == info_ziawang["name"]    	// true

```


## Map对象的属性
- m.size Map对象的元素个数

## Map对象的方法
#### 增
- m.set(key, value)
	- 将键值对添加到Map对象中

#### 删
- m.clear()
	- 清除映射对象中的所有元素，即将map对象变成空映射
	
- m.delete(key)
	- 从映射中删除指定元素


#### 改
- m.set(key, value)
	- 修改key对应的值，其实本质上就是覆盖已有的key-value键值对


#### 查

- m.get(key)
	- 返回Map对象中key对应的value

- m.keys()
	- 返回存放key的迭代器

- m.values()
	- 返回存放value 的迭代器	

- m.has(key)
	- 判断映射中是否包含指定key的元素，如果是则返回true

- m.toString()
	- 返回映射的字符串表示形式

#### 使用中括号操作Map对象的方法和属性
- 偶然发现使用中括号也可以调用Map对象的内置方法，但是一定要注意，对于Map对象内保存的key-value键值对，是不能通过"句点符"`.`和"中括号"`m["xx"]`来访问的，但是Map内置的属性和方法可以

###### 操作属性
- `map_obj["attribute"]`

```javascript
info_ziawang["size"]				// 1
```

###### 操作方法 
- `map_obj["method_name"](args)`

```javascript
m["has"]("name")					// true

m["delete"]("name")					// true

m["size"]							// 0
```


