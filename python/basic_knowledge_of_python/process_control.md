# 流程控制

[点击查看我整理的流程控制思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/process_control.png?raw=true)

## if条件控制
- 一个if语句的完整格式
	- 条件语句可以是一个逻辑判断，也可以更简单的数据如用非空列表表示True，用0表示Flase

```
if 条件语句：
	执行语句
elif 条件语句：
	执行语句
elif 条件语句：
	执行语句
else :
	执行语句
```

> 一旦有一个条件满足并执行之后，条件语句中后面的语句将不会执行

	- 条件语句的优化
	-** 降低代码的重复**
	1. 条件是否逻辑上重复
	2. 语句上是否重复
	- 举个栗子


```python
score = int(input('==>  :  '))                              # 优化前

if score >= 90:
    print('A')
elif score >= 80 and score <90:				#条件也可以改成    80 <= score < 90
    print('B')
elif score >= 70 and score < 80:
    print('C')
elif score >= 60 and score < 70:
    print('D')
elif score < 60:
    print('E')


score = int(input('==>  :  '))

if score >= 90:					#优化之后，因为执行完第一句未执行时，score已经满足了<90的条件
    print('A')
elif score >= 80 :
    print('B')
elif score >= 70 :
    print('C')
elif score >= 60 :
    print('D')
elif score < 60:
    print('E')

```

## while 循环
- 格式：


```python
while 条件：
	循环体的代码1
	循环体的代码2
	循环体的代码3
```

- 死循环

```python
while True :
	print('ok')

while 1:
	print('ok')
```

## for语句（不依赖于索引，对于无序的对象也可以进行遍历）
- for语句依据任意序列中的子项，按照他们在序列中的顺序来进行迭代
- **注意**
	- 尽量不要在序列被迭代的过程中去修改序列，因为处于迭代过程中的序列被修改的话，其性质可能会对序列的性质产生影响，到只剩下的迭代可能会出错**（死循环）**，如果想修改迭代的序列，可以对该序列的副本进行迭代，将
	- 对于无序对象，for循环遍历出的也是无序的；对于有序对象，for循环遍历出来的就是有序的

- **for 循环后，其中的变量 i  ，在内存中仍然存在 **

```python
>>> l = [1, 2, 3, 4, 5, 6]
>>> for i in l[:]:
	if i == 5:
		l = l +['haha','xixi']

		
>>> l
[1, 2, 3, 4, 5, 6, 'haha', 'xixi']
>>> i
6
>>> 
-------------------------
l = [1,2,3]

for i in l:
    l.append(i**3)
print(l)                          #死循环


```


## range()函数
- range(start, end, step)
	- 所生成的链表中不包括范围中的结束值。可以让  range()  操作从另一个数值start开始，也可以指定一个不同的步长step（甚至是负数），但是step的正负要和start到end的指向相匹配，不然会输出空链表。
	- Note: 
		- python2中，range生成一个列表
		- 而在python3中，生成的是一个range对象，不过你可以通过工厂函数来更灵活的生成各种类型序列

```python
Python 2.7.13 (v2.7.13:a06454b1afa1, Dec 17 2016, 20:53:40) [MSC v.1500 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> range(10)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> exit()

C:\Users\Zia Wang>python3
Python 3.6.0 (v3.6.0:41df79263a11, Dec 23 2016, 08:06:12) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> range(10)
range(0, 10)
>>> type(range(10))
<class 'range'>
>>>
```

- 利用工厂函数处理range对象获取任意类型序列，注意str()的结果，得到的并不是一个序列

```python
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> tuple(range(10))
(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
>>> str(range(10))
'range(0, 10)'
>>> 
```

- python3中，利用for对range对象进行遍历


```python
>>> l=[]
>>> for i in range(1, 100, 5):
	l.append(i)

	
>>> l
[1, 6, 11, 16, 21, 26, 31, 36, 41, 46, 51, 56, 61, 66, 71, 76, 81, 86, 91, 96]
>>> 

>>> for i in range(1, 100, -1):
	print(i)

	
>>>      # 输出为空
```
- range()函数并不能真正构造列表。（但是在python2中是可以创建list）
	- ** range Object is not an iterator**
	- 不信你看下面
```python
>>> print(range(10))
range(0, 10)
>>> 
``` 
	
## 迭代器for 和 list
- list可以从可迭代对象中创建列表

```python
>>> l = list(range(10))
>>> l
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> l = []
>>> for i in range(10):
	l.append(i)

	
>>> l
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> 
```

## break、continue、else   
**应该尝试在循环中使用continue、break等来解决复杂逻辑**
- 在循环中
	- 当break被执行时，程序会退出最近的一级for或while循环(即跳出本层循环)
	- 当continue被执行时，程序会跳出当前这次循环，直接开始下一次循环
	-  **continue的条件范围是一个区间的时候，变量的自增要放在continue所在的语块中**
	- continue 如果放在了循环语句中的最后一句，那么continue将变得毫无意义
```python
count = 0

while count<10:
    if  4 <= count <= 6:
        count += 1          #如果没有  count += 1  当count等于4之后程序会陷入死循环
        continue
    print(count)
    count += 1

```
```python
>>> i = 0
>>> while i < 20:
	if i>10:
		break
	print(i, end=" ")
	i = i+1

	
0 1 2 3 4 5 6 7 8 9 10 
>>> i = 0
>>> while i<=20:
	i = i+1
	if 10<i<15:
		print('continue 跳出当次循环，执行下一次循环')
		continue
	print(i)

	
1
2
3
4
5
6
7
8
9
10
continue 跳出当次循环，执行下一次循环
continue 跳出当次循环，执行下一次循环
continue 跳出当次循环，执行下一次循环
continue 跳出当次循环，执行下一次循环
15
16
17
18
19
20
21
#换一种输出方式
>>> i = 0
>>> while i<=20:
	i = i+1
	if 10<i<15:
		print('continue 跳出当次循环，执行下一次循环')
		continue
	print(i,end='  ')

	
1  2  3  4  5  6  7  8  9  10  continue 跳出当次循环，执行下一次循环
continue 跳出当次循环，执行下一次循环
continue 跳出当次循环，执行下一次循环
continue 跳出当次循环，执行下一次循环
15  16  17  18  19  20  21  
>>> 
```
- python中循环语句有一个可选的else分支（类似if语句和try语句），这个else语句会在循环迭代正常完成之后执行。换句话说，它在循环迭代完整个列表（对于for）或执行条件为False（对于）while时执行，但循环被break终止的情况下不会执行。（在最后执行，并且只有在while循环中的else语句在循环没有被break打断的情况下执行）
```python

for i in range(10):
    pass
else:
    print('循环正常结束，else语句执行')

for i in range(10):
    if i==5 :
        break
    pass
else:
    print('循环不正常结束，else不会执行')

for i in range(10):
    if i==5 :
        continue
    pass
else:
    print('循环正常结束，else语句执行')
```

```python
# test.py
for num in range(2, 10):
    if num % 2 == 0:
        print("Found an even number ",num)
        continue
    print("Found a number", num)
```
> 运行结果
```python
>>> 
================= RESTART: C:/Users/Zia Wang/Desktop/test.py =================
Found an even number  2
Found a number 3
Found an even number  4
Found a number 5
Found an even number  6
Found a number 7
Found an even number  8
Found a number 9
>>> 
```

### 代码优化（优化前与优化后代码都有）


```python
#0 1 2 3  7 8 9

# count = 0
# while count<10:
#     if 4 <= count <= 6:
#         print('continue 退出本次循环')
#         count += 1
#         continue
#     print(count)
#     count += 1

# OLDBOY_AGE = 56
# count = 0
# while count<3:
#     age = int(input('==>'))
#     if age > OLDBOY_AGE :
#         print('太大了')
#         count +=1
#     elif age < OLDBOY_AGE:
#         print('太小了')
#         count += 1
#     else :
#         print('猜对了')
#         break
# #       代码优化看下面
# OLDBOY_AGE = 56
# count = 0
# while count<3:
#     age = int(input('==>'))
#     if age > OLDBOY_AGE:
#         print('太大了')
#     elif age < OLDBOY_AGE:
#         print('太小了')
#     else :
#         print('猜对了')
#         break
#     count += 1            #把count += 1 放到后面不影响程序
#          如果改成无限循环形式
# OLDBOY_AGE = 56
# count = 0
# while True:
#     if count == 3:
#         print('错误次数用完了')
#         break
#     age = int(input('==>'))
#     if age > OLDBOY_AGE :
#         print('太大了')
#     elif age < OLDBOY_AGE :
#         print('太小了')
#     else :
#         print('猜对了')
#     count += 1
# OLDBOY_AGE = 56
# count = 0
# while True:
#     if count > 2:
#         break
#     age = int(input('==>'))
#     if age > OLDBOY_AGE:
#         print('太大了')
#     if age < OLDBOY_AGE:
#         print('太小了')
#     if age == OLDBOY_AGE:
#         print('猜对了')
#         break
#     count += 1


# 获取分数等级
# while True :
#     score = int(input('==>'))
#     if score >= 90 :
#         print('A')
#         break
#     elif score >= 80 :
#         print('B')
#         break
#     elif score >= 70 :
#         print('C')
#         break
#     elif score >= 60:
#         print('D')
#         break
#     else :
#         print('E')
#         break


```


## 关于退出两层循环