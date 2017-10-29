# 可迭代对象iterable

## 可迭代对象介绍
- 在JavaScript中，可迭代对象即可以使用`for ... of ..`循环来遍历元素的对象
- JavaScript中由的数据由于可以通过索引操作，因此可以使用while循环来进行遍历，对于Map、Set等没有索引的对象，JavaScript提供了`for ... of`方法。
	- 即可以不依赖索引地遍历集合（在JavaScript中集合是广义的，指array，Map，Set等用于存放数据的容器）

## 可迭代对象有哪些
- JavaScript中的Array、Map、Set都属于Iterable类型


## 可迭代对象的特点
- 可以不依赖索引遍历取值



## 可迭代对象的操作
- `for . . . of` 循环遍历
	- 注意：
		- 对于Map映射对象来说，遍历的到的是一个数组对象，存放了Map对象的key-value对
		- 对于Array和Set对象来说，便利返回其中的一个个元素对象

```javascript
var iter_map = new  Map([['name', 'ziawang'], ['age', 20], ['hobby', ['learning', 'music']]]);
for (var i of iter_map) {
    console.log(i);
}

var iter_set = new Set(['wangzihao', 20, 'non-marriage', 'has girlfriend']);
for (var i of iter_set){
    console.log(i);
}

var iter_array = ['ziawang', 20, 'non-marriage', 'has-girlfriend'];
for (var i of iter_array) {
    console.log(i);
}
```


- 使用iterable对象的`forEach`方法迭代对象
	- 对于Array对象，由于存在索引，因此回调函数的参数依次为`element, index, array`
	- 对于Set对象，由于不存在索引且不存在键值对结构，因此其回调函数的参数依次为`element, sameElement, set`
	- 对于Map对象，由于存在键值对，因此其回调函数参数以此为`value, key, map`
	- 注意，以上回调函数参数可以只传入在执行语句中需要的值



```javascript
// forEach()语法结构

iter_obj.forEach(callback(value, key, iterable_obj), this){
	执行语句;
}

// forEach每遍历一项，就执行一次callback值

```

```javascript
// 方式一，直接创建匿名函数作为回调函数
var iter_set = new Set(['wangzihao', 20, 'non-marriage', 'has girlfriend']);
iter_set.forEach(function (element) {
    alert(element);
})

// 方式二，降低耦合性，只向forEach中传入函数名


function my_callback(value) {
    alert(value);
}

var iter_set = new Set(['wangzihao', 20, 'non-marriage', 'has girlfriend']);
iter_set.forEach(my_callback);
```



[](http://www.jianshu.com/p/0534746020a2)

## 可迭代对象要注意的地方

```javascript
var arr_li = document.getElementsByTagName('li');
console.log(arr_li);
var s = new Set(arr_li);        // 列表貌似不支持forEach

s.forEach(function (value, index) {
    value.innerText = 'AAA';
});

```