# sort函数
> 排序最终都是要比较两个值的大小


## 数组的sort方法
- 在JavaScript中，数组的sort方法默认是按照ASCII码大小对数组中的元素进行比较的，因此sort方法在调用的时候提供了一个参数（一个可以被调用的函数对象），我们可以自定义排序规则，将其抽象画成函数对象传递给sort函数去调用
- `arr.sort(compare_func)`
	- `compare_func`的判断机制
		- 如果自定义的规则判断结果是 x > y，则返回一个1，sort会认为x > y成立，然后将x排在y前面
		- 如果自定义的规则判断结果是x < y，则返回一个-1，sort会认为x < y成立，然后将x排在y后面
		- 如果自定义的规则判断结果是x = y，则返回一个0，sort会认为x与y相等（此时两者相同，谁在前后也无所谓了）


```javascript

var arr_sort = [10, 20, 3, 3, 40, 5, 6];

function my_compare(x, y) {
    if (x > y) {
        return 1;
    } else if (x < y) {
        return -1;
    } else {
        return 0;
    }
}

res_sort = arr_sort.sort(my_compare);
console.log(res_sort)					// [3, 3, 5, 6, 10, 20, 40]
```