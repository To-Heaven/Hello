# 流程控制

## if...else if...else 判断句
- 结构

```javascript
if (判断条件){
    执行语句;
}else if (判断条件 ){
    执行语句;
}else {
    执行语句;
}

``` 

- 注意，当执行语句只有一条语句的时候，可以不加花括号，但是不建议这样做

```javascript
var num_age = 25;
if (num_age <= 18){
    alert('too young too simple');
}else if (num_age>18 && num_age < 30 ){
    alert('young too');
}else {
    alert('old');
}

// -----------------------------------------------------------------
var num_age = 18;
if (num_age <= 18)
        alert('young');
else  if (num_age>18 && num_age<30)
        alert('adult')
else
        alert('old')
``` 



```javascript
    var num_height = parseFloat(prompt('请输入身高(m):'));
    var num_weight = parseFloat(prompt('请输入体重(kg):'));
    var num_bmi = num_weight / (num_height * num_height);

    if (num_bmi <= 18.5){
        alert('过轻')
    }else if (num_bmi < 25){
        alert('正常')
    }else if (num_bmi < 28){
        alert('过重')
    }else if(num_bmi < 32){
       alert('肥胖')
    }else {
        alert('严重肥胖')
    }
``` 



## while 循环
- 在每次循环开始的时候判断条件，然后再决定是否运行循环体中的代码
- 结构

```javascript
while (循环条件){
    循环体;
}

// ------------------------------
var i=1, num_sum=0;

while (i <= 1000){
    num_sum += i;
    i++;
}
alert(num_sum)
```


## do . . . while 
- 在每一次循环体运行结束时判断循环条件，然决定是否开始下一次循环
	- 循环体至少循环一次
- 结构

```javascript
do{
    循环体;
}while (循环条件)

// ----------------------------
var i = 3;

do{
    alert('hello')
    i--
}while (i>0)
```



## for 循环
- 结构

```javascript
for (初始条件;判断条件;递增/减条件){
	循环体;
}
```



## for in  遍历
- 常用来遍历数组、对象（遍历对象的属性，其实array的每一个元素的索引被认为数组的属性）
- 结构

```
for (var i in container){
	执行语句;
}

// -----------------------------
var arr_num = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
for (var i in arr_num){
    val = arr_num[i];
    alert(val);
}
```

## 死循环
- for实现死循环

```javascript
for (;;){
	执行语句;
}
```

- while 实现死循环

```javascript
while (1){
	执行语句;
}
```