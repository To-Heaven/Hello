## 数组
#### 创建方式
- 直接通过方括号创建

```javascript
var arr_num = [1, 2, 3]
console.log(typeof arr_num);		// object
```


- 实例化Array对象创建
	- 不指定参数的时候创建空数组

```javascript
var arr_cls = new Array(1, 'haha', undefined, null, true);
console.log(typeof arr_cls);		// object

// 注意下面的数据只有一个元素，Array(*args)
var arr = new Array([1,2,3]);	// [[1, 2, 3]]

```

- 注意，还有一种创建数组的方式，在实例化数组对象的时候只声明数组的长度，然后在需要的时候通过对索引赋值的方式给数组添加元素
	- 对于该空数组，JavaScript会默认每一个元素为undefined类型


```javascript
var arr_num = Array(5);
console.log(arr_num);			// [undefined × 5]
arr_num[0] = 1;
arr_num[8] = 1;
console.log(arr_num);			// [1, undefined × 7, 1]
```

#### 表示形式
- 使用方括号包含起来
- **数组中的数据可以是JavaScript的任意数据类型**

```javascript
var arr_my = [1, 'ziawang', '', true, undefined, ['2', 0, null]];
console.log(arr_my);		// 1, "ziawang", "", true, undefined, Array(3)]
arr_my[5];				//  ['2', 0, null]
arr_my[-1];				// undefined   -1不会得到['2', 0, null]
arr_my[4];				// undefined

```

#### 特点
1. 可以通过索引来访问数组中存放的元素
2. **索引越界在JavaScript中不会报错**
	- 索引操作时会返回undefined
	- 使用`slice()`切片的时候，索引越界会返回字符串切片的到的最大长度
	
3. **索引赋值时，如果赋值的宽度大于数组长度，数组长度会变长，数组的最大长度会变成索引越界的长度，中间未填充的值用`undefined代替`**


#### 常用操作
- 使用索引赋值
	- `arr[0] = 1`
- 遍历
	- 使用`for in `遍历的得到的是数组的索引，因为`for in `遍历得到的是对象的属性，而索引就是数组的属性
	- 使用`for of` 遍历的到的就是数组的值

###### 注意
1. JavaScript中的数组**不支持**向python那样`l[1: 8: 2]`的切片方式，但是JavaScript提供了`arr.slice()`方法让我们可以实现这个功能
2. JavaScript中**不能**使用`in`判断某元素是否在数组中，我们可以这样实现
	- 注意下面这种情况不要直接使用`a.indexOf(b)`，因为b指向的对象和a内部那个与b存放相同键值对的对象不是同一个对象，即内存中地址不同的两个对象

```javascript
# 比如判断一个对象b是否在数组a中
var a =[{id:1},{id:2},{name:'cc'}];
var b = {id:1};

console.log(JSON.stringify(a).indexOf(JSON.stringify(b))!=-1);
```





#### 数组的属性
- a.length
	- 数组的长度，包含的元素个数
	- 注意
		- **给数组的length属性赋值会改变数组的长度**

```javascript
var arr_my = [1, 'ziawang', '', true, undefined, ['2', 0, null]];
arr_my.length = 4;
console.log(arr_my);		// [1, "ziawang", "", true]
```

- **可以通过对索引重新赋值来修改数组中元素的值**
	- 注意
		- 索引赋值时，如果出现了索引越界，那么数组会在最后一个元素与越界的索引之间插入对应个数的`undefined`

```javascript
var arr_num = [0, 1, 2, 3, 4, 5];
arr_num[8] = 8;
console.log(arr_num);				// [0, 1, 2, 3, 4, 5, undefined, undefined, 8]
```

#### 常用方法
###### 增
- a.push(value1, value2...)
	- value可以是任意类型
		- 如果是容器类型比如数组，就会将数组中的元素一个个添加要现有数组中
	- Appends new items to an array, and returns the new length of the array 将多个element添加到从后面按顺序添加到数组中，并返回数组的长度




- a.unshift([start, count,] value1, value2...)
	- value可以是任意类型
		- 如果是容器类型比如组，就会将数组中的元素一个个添加要现有数组中
	- Inserts new elements at **the start of an array.**
		- 如果不执行start和count，就会在数组开始位置插入值
	- 从start指定的位置开始移除count指定数量的元素，在移除元素的位置插入指定的元素item
	- 返回值为数组的最新长度




###### 删


- a.pop() 
	- Removes the last element from an array and returns it 将数组最后一个元素移除并返回
	- 对于空数组，继续pop会返回`undefined`


- a.shift()
	- Removes the first element from an array and returns it.
	- 移除并返回数组的第一个元素










#### 修改自身并返回

- a.reverse()
	- 反转数组中的元素，数组本身会被修改并返回


- a.sort(comparefunc)
	- Sorts an array.
	- 默认的使用ASCII排序
	- comparefunc：指向我们排序逻辑的函数，比如传递了一个`func`，其参数`(a, b)`，如果返回值的布尔值为`True`，就认为**a>b，就把a放在b的右侧**，如果返回的布尔值为`False`，就认为**a<b，就把a放在b的左侧**
	
```javascript
// 举一个例子

function mySort(a,b)
{
 return a - b;
} 

var arr = new Array(5, 4, 3, 2, 1); 
arr.sort(mySort)
```







#### 查
- a.indexOF(value)

#### 处理数组
- a.slice(start, stop)
	- 不传入参数的情况下，默认截取所有元素。用来复制数组
	
- a.splice(start, deleteCount, value, ...)  插入、删除或替换数组的元素
	- deleteCount：要删除的个数
	- start: 要开始删除的元素的位置
	- value: 需要替换元素时指定
	- 返回值
		- 如果只指定了start参数：**不会修改原数组，返回值为从原数组中截取出的一段子数组**
		- 如果指定了start、deleteCount、*value，会修改原数组，并返回一个空数组
		- 如果指定了start、deleteCount会修改原数组，并返回从原数组中移除掉的子数组

```javascript
obj.splice(1,0,val) 指定位置1插入元素
obj.splice(2,1,val) 指定位置2替换元素
obj.splice(3,1)     指定位置3删除元素
```


#### 返回对象，原数组不变
- a.concat(value1, value2...)
	- value可以是任意类型
		- 如果是容器类型比如组，就会将数组中的元素一个个添加要现有数组中
	- 把参数中的元素添加到数组中，如果参数中有数组，那么就会将数组中的元素添加到当前数组中，而不是将整个数组当作一个元素添加到当前数组中

```javascript
var arr_test = [0, 1, 2];
var arr_concat  = arr_test.concat(3, 4, ['haha', 'xixi']);
console.log(arr_concat);	// [0, 1, 2, 3, 4, "haha", "xixi"]
```

- a.join(seperator)
	- **将数组中的元素用指定字符连接起来拼接成字符串，并返回该字符串**
	- seperator: 字符串，用来连接数组中过的元素

- a.map(func)
	- 将数组a中的元素经过func处理之后存放到一个新的数组中返回