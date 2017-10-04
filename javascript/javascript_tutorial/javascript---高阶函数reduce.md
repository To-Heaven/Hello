## reduce 函数
- 数组对象也有对应的reduce方法`arr.reduce(func)`
	- reduce方法将每次调用func的结果和序列的下一个元素作为参数传入到func中继续做运算。
	- func必须接收两个参数	

```javascript
// 使用arr.reduce对数组中的元素求积/和
    var arr_num = [1, 2, 3, 4, 5, 6];
    
    function multi(x, y) {
        return x * y;
    };

    function add(x, y) {
        return x + y;
    };

    multi_res = arr_num.reduce(multi);
    add_res = arr_num.reduce(add);
    console.log('multi:',multi_res);
    console.log('add:',add_res)
```

- reduce作为返回值

```javascript
var arr_num = [1, 2, 3, 4, 5];

function sum_arr(arr) {
    return arr.reduce(function (p1, p2) {
        return p1 + p2
    })
}

res_arr = sum_arr(arr_num);
console.log(res_arr);
```