# 对象

## JavaScript中的对象
- 在js中，对象的属性和方法以键值对的方式储存在一对花括号中。
	- 注意，每一个属性与属性值键值对之间要用逗号`,`隔开，最后一个键值对不需要添加逗号

```javascript
var obj_myself = {
    name : 'ziawang',
    age : 20,
    hobbies : ['python', 'read', 'eat', 'play'],
    marriage : undefined,
    'has-girlfriend' : true
};
```



## 对象属性
- 对象的属性和属性值是存放在花括号中的一组键值对，键值对中的属性有两种形式
	1. 变量名形式。这种形式的属性名必须遵守JavaScript标识符的命名规范
	2. 字符串形式。当属性名包含了不符合JavaScript标识符规范的命名时，应该用引号将属性名包含起来，将属性名声明为字符串的形式.
		- 本质上，对象的属性其实都是字符串
		- 符合JavaScript标识符命名规范的属性名也可以声明为字符串的形式
		- 在属性名的命名时，属性名应符合JavaScript标识符的命名规范
	
- 访问对象属性  
	1. 直接使用`obj.attribute`的方式访问，这种方式和python中访问对象属性的方式相同
	2. 使用`obj['attribute']`的方式访问

## 增加对象属性
- 直接通过赋值的方式就可以为对象增加属性和属性值

```javascript
var obj_dog = {};

obj_dog.name = 'bobo';
obj_dog.age = 0.5;
obj_dog.favorite = 'gutou';

obj_dog .wang = function wang() {				// 为对象创建方法
    console.log('汪汪汪')
}
```



## 删除对象的属性
- 使用`delete obj.attribute` 或者`delete obj['attribute']`


##  in
- 使用成员运算符`in `可以判断对象的属性是否存在
	- `'属性值' in object`

```javascript
var obj_dog = {};

obj_dog.name = 'bobo';
obj_dog.age = 0.5;
obj_dog.favorite = 'gutou';

bool_hasattr = 'name' in obj_dog
console.log(bool_hasattr)
```


## 对象的继承
- 必须要注意的一点，所有存在于JavaScript中的对象队中都是继承自根对象object。
- 使用 `obj.hasOwnProperty('attribute')` 判断属性是否是对象自有的



```javascript
var obj_dog = {
    name : 'bobo'
};
console.log(obj_dog.hasOwnProperty('name'));
console.log(obj_dog.hasOwnProperty('sayHello'));
```



## 对象通用的方法
- 待补充


## 注意
- 访问一个不存在的属性会返回一个`undefined`，不会报错
- 对象的属性可以是JavaScript中任意类型的数据
- 存放在集合中的对象属性是无序的