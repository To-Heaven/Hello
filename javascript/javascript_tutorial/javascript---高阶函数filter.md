# filter函数
- 数组对象的filter方法接受一个函数作为回调函数，filter用于过滤数组中满足回调函数的元素，所谓满足回调函数，其实是通过将元素作为参数传入回调函数时，返回值的true/false判断的。
	- 回调函数的返回值为true，那么元素将会被保留
	- 回调函数的返回值为false，那么该元素被舍弃

- 这个回调函数有三个可选的参数
	- element  元素值
	- index   索引
	- self   数组本身

```javascript
var arr_num = [1, 2, 3, 4, 5, 6, 7];


res = arr_num.filter(function (element) {
    return element % 2 == 1;
});

console.log(res);
```



- 注意
	- **调用filter方法的数组并没有改变，filter方法会返回一个新的数组**
	- 有时为了增强可阅读性，可以将函数定义好之后，在调用时指向filter中传入函数名。降低耦合性

```javascript
var arr_num = [1, 2, 3, 4, 5, 6, 7];

function get_odd(element) {
    return element % 2 == 1;
};

res = arr_num.filter(get_odd);
console.log(res);
```