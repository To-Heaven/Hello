## 对象的方法
- 之前说到，对象由属性名-属性值、方法名-方法构成。在对象中定义方法的时候，可以直接在花括号中创建

```javascript
var ziawang = {
    name:'ziawang',
    birth_year:1994,
    age:function () {
        var year_today = new Date().getFullYear();
        return year_today - this.birth_year
    }
};

ziawang_age = ziawang.age()
console.log(ziawang_age)
```

- 这样明显可阅读性太差，不如将函数提取出来，在将函数绑定给对象方法名。**但是，这样会遇到坑。因为在对象内部创建方法，使用this关键字的时候，this关键字就代表ziawang对象本身，如果在对象外部创建函数，那么this关键字代表的就是全局对象window**
	- 如果把定义在外部的函数绑定为对象的方法，那么通过对象属性直接调用该方法的时候，`this`指向的是对象而不是window
		- 所谓直接调用，指的是`ziawang.age()`
	- **对于非直接调用方法，不管是直接在对象内定义方法还是在外部定义完再绑定到对象，this指向的都是window，这个漏洞坑的让人怀疑人生**
		- 非直接调用`var f = ziawang.age(); f(); // NaN`

```javascript
function getAge() {
    var year_today = new Date().getFullYear();
    return year_today - this.birth_year
}

var ziawang = {
    name:'ziawang',
    birth_year:1994,
    age:getAge
};

ziawang_age1 = ziawang.age();
console.log(ziawang_age1);				// 23

ziawang_age2 = getAge()
console.log(ziawang_age2)				// NaN，单独调用函数getAge，this指向window
```

## 总结this的坑
- 对于对象绑定的方法，如果是非直接调用，那么js的漏洞会让this指向window。
- 即使使用了strict模式，如果在方法内部定义的内层函数中又调用了this关键字，那么这个this会指向undefined，如果是非strict模式，就会指向mode
- 还有一个不算是语言设计的坑，属于常犯的逻辑错误。就是如果在对象外部定义的函数内部使用了this关键字，this指向window，strict下指向undefined


## 使用apply和call
- 解决上述方法，可以使用JavaScript中函数对象的apply和call方法
- apply(obj, array)
	- obj代表函数中this指向的对象
	- array作为数组用于为函数传参
		- 参数需要以数组的形式传入到函数

```javascript


```


- call(obj, argument1, argument2, ...)
	- obj代表函数中this指向的对象
	- argument为函数的参数，按位置顺序传参而不是通过数组

```javascript

```



## strict模式下
- 上面的坑其实就是JavaScript语言设计的不足，于是针对JavaScript语法的坑，ES5开始，出现了strict mode，这种模式下，会消除js代码中的不足之处，另外据说还能提高编译速度。。。
- 如何使用strict mode？
	-针对整个脚本使用strict mode
		- 在每一个js脚本开始的时候，添加`"use strict";`语句
	-在指定函数内使用strict mode
		- 函数都有自己的明明空间，因此还定义函数时，在函数体第一句就添加上`"use strict";`就可以对指定函数使用strict mode严格模式
- strict模式下，有好多语言漏洞都被规范化或者直接禁用了，比如该模式下
	- 全局变量必须显示声明，否则报错。也就是说变量定义时，不管是否是全局变量，都要声明
	- 禁止this关键字指向全局对象(window)，对于定义在对象外的函数来说，this关键字就指向了`undefined`


## strict mode下的代码重构
- strict虽然能够让this不在指向window，但是this还是没有指向真正正确的对象。这个时候都用到了重构
	- 重构有一点要注意的地方，如果方法的内层函数也调用了this关键字，那么this指向的是window

```javascript


```


