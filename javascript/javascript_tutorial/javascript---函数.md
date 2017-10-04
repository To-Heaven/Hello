# 函数
- 函数其实本质上就是一种抽象，创建函数之后我们在需要实现其功能的时候直接调用该函数就行了，而不需要去重复地完成函数的实现过程

## 创建函数
- 第一种创建函数方式

```javascript
function func_abs(num) {
    if (num < 0) {
        num = -num;
    }
    return num;
};

res = func_abs(-5);
console.log(res);
```


- 第二种，通过匿名函数创建函数
	- 匿名函数实际上就是没有函数名称的函数
	- 创建完匿名函数，可以定义一个变量指向该匿名函数的内存空间，通过该变量就可以访问该匿名函数

```javascript
var func_abs = function (num) {
    if (num < 0){
        num = -num;
    }
    return num;
};

res = func_abs(-5);
console.log(res);
```

## 函数的参数
- 函数的参数写在圆括号内，多个参数用逗号隔开

## return关键字
- 调用函数的结果通过`return`关键字返回，函数一旦运行到了`return`拿到返回值，函数调用就结束了。因此`return`关键字之后的代码是无效的
- 如果函数`return`后没有跟任何值，或者函数内根本就没有`return`关键字，那么函数调用得到的结果就是`undefined`
	- `return ;  `  <==> `return undefined`  <==> 不写return


## 函数传参
- JavaScript中调用函数传参的时候，传入的参数个数可以多余函数定义时的形参个数，也可以少于函数定义时的形参个数
- 每一个函数对象都有一个`func.length`属性，代表了函数期望输入的实参个数
	- 如果传入的实参个数大于 该属性值，多余的参数会被JavaScript忽略
	- 如果传入的参数小于该属性值，那么JavaScript会默认传入`undefined`作为函数的实参


## JavaScript中的可变参数arguments
- **arguments只能在函数内部起作用**，相当于把函数调用时传入的参数打包起来，在函数中可以通过遍历或索引的方式获取参数并进行操作。


```javascript
function func_add(nums) {
    var sum = 0;
    for (var i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
}

res = func_add(1, 2, 3, 4);
console.log('add_result: ',res)
```

- 即使定义函数的时候不定义任何形参，在调用函数时仍然可以传入实参，并通过arguments来操作

```javascript
function func_sum() {
    if (arguments.length == 0) {
        return 0;
    }
    var sum = 0;
    for (var i = 0; i < arguments.length; i++){
        sum += arguments[i];
    }
    return sum;
}

res_non_arg = func_sum();
console.log('res_non_arg: ', res_non_arg);

res_one_arg = func_sum(5);
console.log('res_one_arg: ', res_one_arg);

res_more_arg = func_sum(1, 2, 3, 4);
console.log('res_more_arg: ', res_more_arg);
```

## 利用arguments判断并重置参数
- 待补充




## 使用rest关键字
- 使用rest关键字可以一次性的接收传入的实参中多余`func.length`的参数。
- rest本质上是一个变量，指向存放了多余实参的数组
- 格式
	- 在定义函数时，在函数参数列表最后加上`...rest`即可
	- **rest关键字一定要放在参数列表的最后**
- 注意
	- 如果rest没有接收到参数，那么rest指向的就是一个空数组 

```javascript
function func_rest(num1, num2, ...rest) {
    console.log(num1);
    console.log(num2);
    console.log(rest);
}

func_rest(1);               // 1 undefined []
func_rest(1, 2);            // 1 2 []
func_rest(1, 2, 3, 4);      // 1 2 [3, 4]
```


## 函数作为返回值

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