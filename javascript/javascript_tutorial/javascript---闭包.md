# 闭包结构
- 在很多语言中都有闭包这个结构。
- 闭包由两部分构成
	- 局部变量
		- 这个局部变量是定义在高阶函数内存空间中的局部变量，它被闭包函数调用，这个局部变量可以用来表示一种状态。
	- 闭包函数
		- 闭包函数作为高阶函数的返回值，调用这个高阶函数的时候，返回的是这个闭包函数，当我们想调用该闭包函数的时候通过这个返回值就可以调用。

> 闭包就是携带了状态的函数


## 闭包的用处
- 在函数外部访问函数内部的变量值
	- 命名空间中，外部命名空间是不能访问内部命名空间中的变量的。但是可以利用闭包结构来实现从函数外部访问函数内部的变量值

```javascript
function get_inner_var() {
    var inner = "this is a inner value";

    function return_inner_var() {
        return inner;
    }
    return return_inner_var
}

var get_inner = get_inner_var();
var inner = get_inner();
console.log(inner);
```

- 保存变量的状态
	- 闭包函数在其内部调用了外部函数的变量，这个变量称为自由变量，外部函数调用结束的时候不会将自由变量一同释放掉，因为该变量被闭包函数调用，即两者绑定在一起，当闭包函数执行完毕之后，该自由变量才会被释放

## 注意
- 闭包函数不要引用循环变量或者后续会发生改变的变量。
	- 因为闭包函数内保存的其实是对自由变量的引用，当如果自由变量是不断变化的，这意味着连续多次调用同一个闭包函数得到的结果可能是不同的
	- 如果这种情况发生在循环中，特别是`var`声明的循环变量，如果闭包函数引用了这个循环变量，那么闭包函数中只会记住循环结束后循环变量的最终值，而不是返回闭包函数时循环变量的值
	- 解决方法
		- 第一种：使用`let`声明循环变量
		- 第二种：为闭包函数绑定循环变量当前的值

```javascript
function arr_func() {
    var arr = [];
    for (var i=1; i<=3; i++){
        function inner() {
            return i*i;
        }
        arr.push(inner);
    }
    return arr;
}

arr = arr_func();
console.log(arr[0]());
console.log(arr[1]());
console.log(arr[2]());

// ========使用 let 代替var=============
function arr_func() {
    var arr = [];
    for (let i=1; i<=3; i++){
        function inner() {
            return i*i;
        }
        arr.push(inner);
    }
    return arr;
}

// =========绑定循环变量当前的值=============
function arr_func() {
    var arr = [];

    function inner(n) {
        return function () {
            return n*n;
        }
    }

    for (var i=1; i<=3; i++){
        arr.push(inner(i))
    }
    return arr;
}


arr = arr_func();
console.log(arr[0]());
console.log(arr[1]());
console.log(arr[2]());
```

- 举个例子

```javascript
　　var name = "The Window";

　　var object = {
　　　　name : "My Object",

　　　　getNameFunc : function(){
　　　　　　return function(){
　　　　　　　　return this.name;
　　　　　　};
　　　　}
　　};

　　alert(object.getNameFunc()());

// ====================================
　　var name = "The Window";

　　var object = {
　　　　name : "My Object",

　　　　getNameFunc : function(){
　　　　　　var that = this;
　　　　　　return function(){
　　　　　　　　return that.name;
　　　　　　};
　　　　}
　　};

　　alert(object.getNameFunc()());

```

