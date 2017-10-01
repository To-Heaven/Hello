# 高阶函数
- 简单来说，高阶函数就是将其他函数名作为参数调用的函数
- 告诫函数的优点
	- 将作为参数传入的函数的运算规则抽象化了，调用者只需要知道传入函数的功能而无需了解其实现细节，提高了开发效率
	- 增强了可阅读性

## 高阶函数map
- 数组对象的map方法`arr.map(func)`
	- 将数组中的元素作为func的参数一个个传入，func的返回值重新组装成一个数组返回给map方法

```javascript
function pow(num) {
    return num * num;
}

var arr_num = [1, 2, 3, 4, 5, 6];
arr_res = arr_num.map(pow);
console.log(arr_res)			// [1, 4, 9, 16, 25, 36]
```

