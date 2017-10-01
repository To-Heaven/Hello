# 变量作用域
> JavaScript的函数是可以嵌套，内层函数可以调用外层函数声明的变量，但是外层函数不能调用在内层函数中声明创建的变量，这一点在几乎所有计算机语言中都有效


## 函数体内声明的变量
- 在函数体内，使用关键字`var`声明的变量只在该函数内部有效。
	- 因此不同函数内即使存在了重名的变量，这些变量之间是相互隔离的，不会相互产生影响
	- 在函数体内可以调用函数体外层定义的变量

```javascript

``` 

- 嵌套函数中的变量作用范围
	- 外层函数不能调用内层函数通过`var`关键字声明的变量
	- 但内层函数可以调用外层函数声明的变量

```javascript

``` 

- **JavaScript函数在调用变量的时候，会首先检查函数本身，如果在函数内部没有找到对应的变量，就会一层一层的向外寻找该变量，直到找到可该变量**


## 变量在函数体中的加载顺序
- 在函数被调用的时候，JavaScript解释器首先会将函数体中声明地`变量名`（而不是变量值）加载到函数的内存空间，当执行到变量的赋值时，该变量才会指向存放变量值对象的内存空间。
- 在函数体构造时，我们可以先调用变量，再声明变量。但是这种情况下调用变量得到的是`undefined`。
- 因此，应该遵循变量先声明后调用的方式来定义函数

```javascript
function func_test_var() {
    var a = 1;
    console.log(a + b);
    console.log('b : ', b)

    var b = 2;
}

func_test_var()

// -------- 等价于 --------------------

function func_test_var() {
    var
            b,
            a = 1;

    console.log(a + b);
    console.log('b:', b);

    b = 2;
}

func_test_var()

```


## JavaScript中的全局作用域
- JavaScript中，为定义在函数体内的变量就存在于全局作用域中。处于全局作用域中的变量可以被函数调用。
- 其实，每一个JavaScript脚本都有一个全局对象`window`，存在于全局作用域的变量都被视为全局对象`window`的一个属性。	
	- 在全局命名空间中定义的函数（顶层函数），其本质就是全局命名空间中的一个变量指向函数体的内存空间，因此函数名也可以作为`window`对象的一个属性。
	- 对于JavaScript内置的函数比如`alert`，`console`等都是可以当作`Window`对象的属性来调用的
- JavaScript中，任何变量如果在当前作用域中额米有找到，就会往上一层继续寻找，如果在全局作用域中也没有找到，就会抛出ReferenceError

```javascript

``` 


## 创建自己的名字空间
- 当我们在不同的JavaScript文件中使用了相同的全局变量时，就会出现明明冲突。
- 解决这个问题的方法就是自定义名字空间
	- 自定义名字空间相当于自定义一片变量的命名空间，可以给这个空间自定义命名，从而避免冲突。
	- 创建命名空间类似于创建一个Map对象，将变量名和变量值分别作为key和value传入到Map对象中，而每一个key又可以看作是Map对象的属性，因此创建名字空间的变量时，就可以通过给属性赋值的方式创建，也可以通过key-value创建
- 注意：
	- 床i教案自己的名字空间的时候，尽量使用全大写字母命名


```javascript
var MYAPP = {};

MYAPP['filename'] = 'test';
MYAPP.chunk_size = 1024;

MYAPP.func = function () {
    console.log(MYAPP.filename);
    console.log(MYAPP.chunk_size);
};

MYAPP.func();
```

## let与var
- 在for循环中，通过`var`是不能创建一个只存在于for循环代码块中的局部变量的，如果初始条件中的`i`变量时用的`var`关键字声明的，那么该变量在for循环结束之后，在for循环外部仍然可以调用，这就是常说的变量溢出。
- 解决上述问题的方法就是使用`let`关键字。
	- 使用let关键字可以在for循环的内存空间中创建一个局部变量，当for循环结束，其内存空间释放的同时，该变量也会被释放。这样既节省了内存空间，又防止了内存溢出


```javascript

for (var i= 0; i < 5; i++){
    console.log(i);
}
console.log(i);          // 变量溢出


for (let j = 0; j < 5; j++){
    console.log(j);
}
console.log(j);         // 变量不会溢出，此处会抛出ReferenceError
    
```