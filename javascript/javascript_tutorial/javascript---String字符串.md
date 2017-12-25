# JavaScript中的字符串

## 表示形式
- JavaScript中字符串用单引号`''`或双引号`""`包含起来的任意文本（Unicode字符、数字、标点）
	- 注意
		- 引号只是表示方式，并不是字符串内容的一部分
		- JavaScript中没有字符类型
		- 转义字符
			- `\n`   换行
			- `\"`    双引号
				- 在双引号包含的字符串中，单引号本身也可以作为一种字符存在于字符串中
			- `\'`     单引号
				- 在单引号包含的字符串中，双引号本身也可以作为一种字符存在于字符串中
			- `\\`     反斜杠
- 多行字符串使用反引号
	- 多行字符串的每一行之间由换行符隔开，可以使用`\`转义字符将换行符转义，使得多行字符串本质上成为一行

```javascript
`
这是
多行字符串
`;
```

## 字符串特点
- 16位
	- JavaScript采用Unicode编码，每一个元素占用的空间是16个bit位，即2个bytes
- 不可变
	- 字符串对象一旦被创建出来就不能再被更改，因此针对某则字符串的方法不会改变原有字符串，而是会返回一个新字符串
- 有序序列


## 创建字符串的两种方式
1. 直接使用引号创建字符串
	- 注意，使用此方法得到的字符串，使用`typeof`判断类型的时候得到的是`string`


2. 实例化String类获得字符串
	- 注意，通过实例化获取字符串的时候要使用`new`关键字
	- 注意，使用此方法得到的字符串，使用`typeof`判断类型的时候得到的是`object` 而不是`string`
	
```javascript
var str_class = new String('ziawang');
var str_yinhao = 'ziawang';
console.log(str_class, typeof str_class);
console.log(str_yinhao, typeof str_yinhao);

console.log(str_class === str_yinhao)   		// 返回的是False，因为一个是字符串String对象，一个是object 对象，在绝对相等条件比较时不会做隐式转换，因此是False
``` 

- 注意
	- 不要使用String实例化的方式创建字符串对象，这回拖慢执行速度
	- 字符串本身就是一个数组，数组是有序的

## 常用操作

#### 字符串拼接
- 可以使用加号`+`将多个字符串连接起来

#### 模板字符串
- 模板字符串中可以用`${变量名}`存放变量，JavaScript会在调用该模板字符串的时候将变量的值替换到模板字符串中
- 注意	
	- 模板字符串必须使用多行字符串包含起来
	- 不要忘了`$`符号 

```javascript
var name = 'ziawang', age = 20;
alert(`hello, my name is ${name}, and I\'m ${age} years old`);
```

#### 索引
- 字符串本质就是一个数组，因此可以通过索引来获取指定位置的字符
	 - `str_name[0]`
- 字符串是不可变的，如果对字符串的一个索引赋值，不会对原字符串产生任何影响，甚至都不会报错

```javascript
var str_name = 'ziawang'
str_name[0] = 'haha'
console.log(str_name);
ziawang
``` 


## 字符串属性——length
- 返回字符串的长度 

## 字符串的常用方法
#### 大小写

- `s.toUpperCase()`
	- 返回一个大写的字符串

- `s.toLowerCase()`
	- 返回一个小写的字符串


#### 查询字符串信息
- `s.charAt(index)`
	- 返回字符串中指定索引位置的字符

- `s.indexOf(subchar)`
	- 返回子字符串中首字符位置所在的索引

- `s.substr(index,  number)`
	- 从指定索引位置开始截取指定number长度的字符串


```javascript 
var str_num = '0123456789';
console.log(str_num.substr(0, 3));			// 012
console.log(str_num.substr(1, 3));			// 123
```

- 切片
	- `s.substring(start, end)`
	- `s.slice(start, end)`

```javascript
var str_num = '0123456789'
console.log(str_num.substring(0, 9));			// 012345678
console.log(str_num.substring(0, 1000));			// 0123456789
console.log(str_num.substring(0, -1));  			// 空字符串， 不支持这种切法

console.log(str_num.slice(0, -1));				// 012345678
console.log(str_num.slice(0, 9));					// 012345678
console.log(str_num.slice(0, 1000));				// 0123456789
```


#### 处理字符串
- `s.repeat(number)`
	- 重复number次字符串

```javascript
var name = 'ziawang';
var name_repeat = name.repeat(5)			// 'ziawangziawangziawangziawangziawang'
```


- `s.split(separator, limit)`
	- return: 返回值为一个数组，存放了切割后的子字符串
	- separator: substring. 用来切割字符串
	- limit: number-or-string_of_number，用来限制 返回数组中子字符串的个数（数组的长度）
		- number 为0或其他非number的值时，返回的是一个空数组
		- limit为字符串，但是字符串中存放的是数字的时候，JavaScript会将字符串转换成数字再确定返回数组的长度

```javascript
var s_test = 'aaa0aaa0aaa0aaa0';
var res = s_test.split('0', '2');
console.log(res)
```

- `s.concat(strings)`
	- 将多个string对象与s拼接

```javascript
var name = 'ziawang', hobby = 'study', site = 'www.ziawang.com';
console.log(name.concat(hobby, site))		// ziawangstudywww.ziawang.com
```

- `s.trim()`
	- 移除字符串首尾空白


#### 正则

	
- `s.search()`
	- 返回匹配字符串的首字符位置索引
- `s.match()`
	- 返回值为一个数组，存放正则表达式一个或多个的匹配
	
- `s.replace()` 
	- 替换与正则表达式匹配的字符串




> JavaScript是弱类型语言，会自动做一些数据类型的隐式转换。从使用者的角度来看，如果一个语言可以隐式转换它的所有类型，那么它的变量、表达式等在参与运算时，即使类型不正确，也能通过隐式转换来得到正确地类型，这对使用者而言，就好像所有类型都能进行所有运算一样，所以这样的语言被称作弱类型
