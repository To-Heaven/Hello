# Date对象

#### 创建一个时间对象
- 创建当前系统时间的时间对象

```javascript
var time_now = Date();
console.log(time_now);		// Mon Sep 25 2017 16:59:19 GMT+0800 (中国标准时间)
```

- 创建指定时间的时间对象
	- 在实例化时间对象时指定参数
	- 参数注意：
		- 年月日之间用空格、逗号、横杠分隔都可以，但是不能用冒号
		- 时间应该用冒号或逗号
		- __月份用数字表示时，0代表1月， 11代表12月__
		
```javascript
var time_specified = new Date(2016, 1, 1, 19, 59, 59);
console.log(time_specified);		// Mon Feb 01 2016 19:59:59 GMT+0800 (中国标准时间)
```

> GMT指Greenwich Mean Time(GMT) 格林威治时间，后面加8代表北京时区时间 

- 通过ISO 0861 规范的时间字符串创建指定时间对象
	- `2016-01-01T19:59:59.888+08:00`
	- 注意
		- 传入的是字符串


```javascript
var time_specified = new Date(`2016-01-01T19:59:59.888+08:00`);
console.log(time_specified);

```

- 通过时间戳创建
	- 获取本地时间的时间戳`Date.parse(time-string)`
	- 获取UTC时间的时间戳`Date.UTC()`
	- time-string为ISO 0861 规范的时间字符串，也可以是`Y-m-d H:M:S`的格式
	- 将时间戳作为参数传入Date()实例化也可以得到对应的时间对象

```javascript
var time_stamp = Date.parse('2016-01-01T19:59:59.888+08:00');
console.log(time_stamp);
console.log(Date(time_stamp));
```


#### 获取字符串形式时间
- `date_obj.toLocalString();`
	- 本地操作系统时间的字符串形式


- `date_obj.toUTCString`;
	- UTC时间，与北京时间之间差8小时



#### date object 方法
- date.getFullYear()
	- 返回完整的数字年份
- date.getMonth()
	- 返回JavaScript数字形式的月份
	- JavaScript有个别扭的地方就是把1月用0数据代替，以此类推，11代表12月
- date.getDate()
	- 返回月份中的天数（即几号几号。。。）
- date.getDay()
	- 返回数字形式的星期
	- 0表示周日，从周日开始计数，以此类推，周六是6	
- date.getHours()
	- 返回数字形式的小时（24小时制）
- date.getMinutes()
	- 返回数字形式的分钟数
- date.getSeconds()
	- 返回数字形式的秒数
- date.getMilliSeconds()
	- 返回毫秒数


- date.getTime()
	- 返回系统当前时间的时间戳


- date.toLocalString()
	- 将实践着换成字符串时间

- date.toUTCString()
	- 将时间转换成字符串形式的格林威治时间

#### module functions
- Date.parser(datastring-or-data_obj)
	- 将一个实践转换成时间戳
	- 这个时间戳可以在Date()实例化时作为参数传入
 




