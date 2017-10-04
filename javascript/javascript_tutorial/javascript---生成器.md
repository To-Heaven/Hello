# 生成器

## 生成器是什么鬼
- 顾名思义，生成器就是用来生成数据的机器，说他是机器一点也不为过，生成器可以用来处理数量非常庞大的数据（或者说处理无法确定大小的数据，比如爬取一个网站，下载一个未知大小的文件等等）
- JavaScript中的生成器和python中的生成器非常相似。

## 生成器的特点
- 生成器本质上是一个函数，普通函数返回一次值之后内存就要释放，而生成器可以返回任意数量的返回值。普通函数中的返回值用关键字`return`，在生成器中，应该用`yield`,当然，在生成器中也可以使用return返回值，但是一旦执行到return关键字，那么就会返回其对应返回值，进而结束生成器的运行。
- 生成器可以用来处理比较庞大的数据量
- `yield`关键字在每返回一次值后，都会冻结生成器函数的当前运行状态，直到下一次调用`next()`


## 创建一个生成器
- JavaScript中创建生成器函数和普通函数比，多了两样东西————`*`和`yield`
	- `*`放在function关键字后面，声明该函数是一个生成器函数`function*`
	- `yield`放在函数体中，其后跟着的是返回值


```javascript
// n以内的斐波那契计算
function* fibo(n) {
    var
        a = 1,
        b = 1,
        temp;

    while (a < n){
        yield a;
        temp = b;
        b = a;
        a = temp + b;
    }
}

var gene_fibo = fibo(100);
for (var num of gene_fibo){
    console.log(num);
}
```

## 从生成器对象中获取值
- 使用生成器对象的`next()`方法
	- 该方法返回一个对象，存放了返回值和生成器的状态信息
		- `{value:xxx, done:true/flase}`
		- value即对应返回值
		- done代表生成器状态
			- false代表生成器未运行结束，还能生产出数据。最后一个值被yield返回时，对象的done属性编程了true，表明生成器不再能生产数据了
			- true代表生成器运行结束，不能再生产处数据了。如果此后再调用生成器的next方法，返回的对象中，value为`undefined`，即`{value:undefined, done:true}`
- 使用`for ... of ...`