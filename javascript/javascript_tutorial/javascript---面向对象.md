#面向对象

## 对象
- [JavaScript对象](http://www.ziawang.com/javascript/javascript_tutorial/javascript---%E5%AF%B9%E8%B1%A1.html)
- 在JavaScript中，使用一对花括号就可以创建一个对象，对象属性是字符串类型。

## 原型对象与类
- 在面向对象的程序设计中，Java、python等语言都会通过类class来创建对象，但在JavaScript中并不存在类class。
- JavaScript中，我们不通过类就可以创建一个对象，但在面向对象程序设计中，对于一类具有相同属性和方法的对象，不可能还通过这种方法创建一个个相似的对象（重复造轮子）。
- JavaScript提供了原型对象的概念，我们可以通过这个原型对象来创建一个新的对象	
	- 原型对象本质上也是一个对象，JavaScript中的任何对象都可以拿来作为原型对象。通过原型对象创建一个对象的时候，会将原型对象的属性，方法等拷贝给新的对象（相当于继承），新对象可以对原型对象的属性和方法进行扩展和覆写（相当于派生）
- 通过原型对象创建对象
	- 使用`new_obj.__proto__ = proto_Obj`创建
	- 使用`Object.create(proto_obj)`创建
		- 单独使用Object.create()创建的对象是完全从proto_obj继承过来的，要覆盖原型对象中的内容，可以将此方法封装进自定义函数中，通过自定义函数创建对象

```javascript
// 使用`new_obj.__proto__ = proto_Obj`创建
var Student = {
    classroom: '三年二班',
    name: undefined,
    say: function () {
        console.log(this.name, '在<', this.classroom, '>学习');
    }
};


var ziawang = {
    name: 'ziawang',
    age: 20
};

ziawang.__proto__ = Student;
ziawang.say();

// =====================================================

//  使用`Object.create(proto_obj)`创建
var Student = {
    classroom: '三年二班',
    name: undefined,
    say: function () {
        console.log(this.name, '在<', this.classroom, '>学习');
    }
};


// var ziawang = Object.create(Student);
// ziawang.say();      // undefined "在<" "三年二班" ">学习"

function createStudent(name) {
    var obj = Object.create(Student);
    obj.name = name;
    return obj;
}

var ziawang = createStudent('ziawang');
ziawang.say();      // ziawang 在< 三年二班 >学习
```

## 原型链
- 原型链本质上是一种关系链
	- 每个JavaScript对象都有一个原型链
- 比如上述例子中的原型链
	- `ziawang --> Student.prototype --> Object.prototype --> null`
- 一个数组对象的原型链
	- `arr --> Arrary.prototype --> Object.prototype --> null`
-原型链与属性查找
	- 调用一个对象的属性时，会顺着原型链来查找属性
		- 首先会在该对象上查找该属性，如果没找到就去其原型对象上查找，以此类推，如果在Object.prototype上也没查找到，那么该属性值就会返回一个undefined
		
## 使用构造函数创建对象
- 面向对象的程序设计中，应该利用构造函数来创建对象。
- 构造函数的作用就类似其他语言中的class类，在构造函数中使用this关键字定义对象的方法和属性   
- 通过构造函数创建对象的时候使用的是`new`关键字，这和我们实例化Date(), RegExp()等时用的方式相同，如果不使用`new`关键字，那么就等同于函数调用，返回给对象一个`undefined`

```javascript
function Student(name) {
    this.name = name;
    this.say_hi = function () {
        console.log(this.name, 'sayed :" Hi ~ ~ "')
    }
}

var ziawang = new Student('ziawang');
var wangdabao = new Student('wangdabao');

ziawang.say_hi();
console.log(ziawang.name);

wangdabao.say_hi();
console.log(wangdabao.name);

console.log(ziawang.say_hi == wangdabao.say_hi);        // False
```

- 定义构造方法的时候，如果将对象的方法也定义在构造方法中，每通过该构造函数创建一个对象，该对象的方法就会存放在该对象的内存空间中，这样显然是浪费内存的。我们可以将此方法定义在构造函数对应的原型对象上，这样对象调用方法时会顺着原型链在Student.prototype中找到该方法。

```javascript
function Student(name, age) {
    this.name = name;
    this.age = age;
}

Student.prototype.say_hi = function () {
    console.log(this.name, 'sayed :" Hi ~ ~ "');
};


var ziawang = new Student('ziawang');
var wangdabao = new Student('wangdabao');

ziawang.say_hi();
console.log(ziawang.name);

wangdabao.say_hi();
console.log(wangdabao.name);

console.log(ziawang.say_hi == wangdabao.say_hi);        // true
```

## 构造函数常用的设计模式
- 为避免创建对象时忘记使用`new`，我们可以创建一个函数将构造函数和`new`封装进去，在创建对象的时候，调用这个函数就可以了

```javascript
function Dog(obj) {
    this.name = obj.name || '嘟嘟';
    this.age = obj.age || '0'
}

Dog.prototype.run = function () {
    console.log(this.name, 'is running ...')
};


function createDog(obj) {
    return new Dog(obj || {})
}


var dog_bobo = createDog({
    name: 'bobo'
});

console.log(dog_bobo.name);
console.log(dog_bobo.age);
dog_bobo.run();
```





## 继承
- JavaScript没有class类，因此继承也只能通过原型链来完成。原型链上的对象是从左往右继承的。因此要让一个对象/原型对象继承自另一个原型对象，只需要在原型链上让被继承的原型对象在其右边就行了。
- 那么问题来了，实现呢？
	- 比如现在有一个原型链`a --> B --> Object.prototype --> null`，创建一个原型对象`M`继承自B。
		- 首先，肯定要继承B函数的构造函数，这个可以在M构造函数中实现
		- 对于B在构造函数外定义的方法，使用上面方法是无法让继承的子对象调用的，即在本质上B并没有后在原型链上处于M的右边
		- 可以创建一个中间原型对象F，让原型对象F指向原型对象B，然后new创建一个F的实例，让该实例作为M的原型对象
	
```javascript
// 
function Animal(obj) {
    this.name = obj.name || '不明生物';
    this.age = obj.age || '0';
}

Animal.prototype.run = function () {
    console.log(this.name, 'is running . . .');
};


function createAnimal(obj) {
    return new Animal(obj || {});
}

function Dog(obj) {
    Animal.call(this, obj);
    this.name = obj.name;
}

Dog.prototype.shout = function () {
    console.log('dog: ', this.name, 'is shouting . . .');
};

function createDog(obj) {
    return new Dog(obj || {});
}

var dudu = createDog({
    name: 'dudu'
});

dudu.shout()		//  dog:  dudu is shouting . . .
dudu.run()          //  TypeError: dudu.run is not a function
```

- 上面例子虽然在Dog构造函数内部调用了Animal的构造函数，但是本质上并没有在实现继承

```javascript
function Animal(obj) {
    this.name = obj.name || '不明生物';
    this.age = obj.age || '0';
}

Animal.prototype.run = function () {
    console.log(this.name, 'is running . . .');
};


function createAnimal(obj) {
    return new Animal(obj || {});
}

function Dog(obj) {
    Animal.call(this, obj);
    this.name = obj.name;
}


function createDog(obj) {
    return new Dog(obj || {});
}


function F() {

}

F.prototype = Animal.prototype;

Dog.prototype = new F();

Dog.prototype.constructor = Dog;


// 此时在new F()上定义方法，此段代码要放在建立好原型链，实现了继承之后
Dog.prototype.shout = function () {
    console.log('dog: ', this.name, 'is shouting . . .');
};


var dudu = createDog({
    name: 'dudu'
});

dudu.shout();
dudu.run();
```

- 在一个脚本中，实现集成可能要用好多次，最好的方法是将上述继承过程封装到函数中，提供简单明了的接口，需要这个功能时直接调用即可

```javascript
function inherit(sub, par) {
    function Temp() {}
    Temp.prototype = par.prototype;
    sub.prototype = new Temp();
    sub.prototype.constructor = sub;
}
```

- 继承顺序
	1. 在子对象中调用父对象的构造函数
	2. 使用中间对象
		1. 将中间对象的原型对象指向要父原型对象
		2. 将子对象指向中间原型对象的一个实例对象
		3. 重新绑定子对象的构造方法
	3. 创建子对象的方法
	
 