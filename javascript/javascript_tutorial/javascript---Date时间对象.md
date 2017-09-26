# Date对象

## 创建一个时间对象
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
	- time-string为ISO 0861 规范的时间字符串
	- 将时间戳作为参数传入Data()实例化也可以得到对应的时间对象

```javascript
var time_stamp = Date.parse('2016-01-01T19:59:59.888+08:00');
console.log(time_stamp);
console.log(Date(time_stamp));
```


## 获取字符串形式时间
- `time_obj.toLocalString();`
	- 本地操作系统时间的字符串形式


- `time_obj.toUTCString`;
	- UTC时间，与北京时间之间差8小时



## date object 方法





