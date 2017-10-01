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

- 这样明显可阅读性太差，不如将函数提取出来，在将函数绑定给对象方法名。**但是，这样会报错。因为在对象内部创建方法，使用this关键字的时候，this关键字就代表ziawang对象本身，如果在对象外部创建函数，那么this关键字代表的就是全局对象window**

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



## strict模式下的代码重构
