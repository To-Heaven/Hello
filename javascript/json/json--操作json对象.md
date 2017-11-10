## 获取json中的数据

```javascript
var info = {
	"name": "ziawang",
	"age": 22,
	"hobbies": ["study", "music", "movie"]
}

```


#### 使用属性方式访问
- info.name


#### 使用中括号访问
- info["name"]

### 循环遍历访问

```javascript
for (var ele in info){
	info[ele] = null;		
}
// 注意，遍历修改json对象内容时，不能使用属性方式修改，如 不能 info.ele = null

```


## 修改json中的数据
#### 使用属性值方式修改
- info.name = "wangzihao"
- 这种方式在循环中不能使用




#### 使用中括号修改
- info["name"] = "wangziaho"


## 删除json中的数据
- 使用`delete`关键字删除
	- `delete info["name"]`
	- `delete info.name`

## 注意
#### 使用字符串存储数据
- json对象内key对应value存储的值的数据类型必须是json指定的那几种，对于JavaScript的Date对象或者其他对象来说，可以先转换成双引号包含的字符串，再存放到json对象中。存放在json对象中的Date对象仍然可以通过Date()转换成Date对象


```html

```

- 同样的，可以将函数及其表达式转换成字符串存放到json对象中，但是不建议将函数存放到json对象中

























 