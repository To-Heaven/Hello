## 再说命名空间
- 局部命名空间
	- 函数的每次运行都会在内存中创建一个局部命名空间，一般情况下，函数运行结束，这个命名空间也会随着函数被释放（闭包）
		- 闭包中应使用`nonlocal`声明
	- 函数内部的任意赋值操作，在默认情况下（没有global声明），都会在函数的命名空间中创建一个对应的变量。

```python
a = [1, 2, 3]


def name_space_test(a):
    print(id(a), a)
    a = a + [4]           # id 不同
    # a += [4]              # id 相同
    print(id(a), a)
```




## 函数传参传递的是对象
- 废话不说，放代码

```python
def change(list_org):
    print(id(list_org))
    if len(list_org) > 5:
        list_org = ['a', 'b', 'c']
    for index_l, value_l in enumerate(list_org):
        if isinstance(value_l, list):
            list_org[index_l] = '***'
    print(list_org)
    print(id(list_org))

test_l1 = [1,  2, 3, 4, 5, 6]
print(id(test_l1))
change(test_l1)
print(test_l1)
print(id(test_l1))

print('++++++++++++++++++++++++++++++++++++++')

test_l2 = [1, 2, [1, 2, 3]]
print(id(test_l2))
change(test_l2)
print(test_l2)
print(id(test_l2))
```

- 在`test_l1`的测试结果中，全局命名空间中的`test_l1`保持不变，而在函数中满足`if len(list_org)>5`条件，将另一个list对象赋值给`test_l1`之后，并没有改变全局命名空间中的`test_l1`，因此函数传参传递的不是引用
- 在`test_l2`的测试结果中，全局命名空间中的`test_l2`，满足isinstance条件之后通过索引赋值，修改了值为列表的元素。发现全局命名空间中的`test_l2`也额比改变了，这说明函数传参传递的也不是值。
- 那么问题来了？按照这两个结果，对于变量的传参，既不是传值也不是传引用，到底是传递的什么呢?
	- 函数传值传入的是整个对象
		- 如果该对象是可变对象，对可变对象内的元素进行修改的时候，python解释器并不会因为此次修改而在内存中创建一个新的可变对象。而对于在函数中创建新的对象并将该对象赋值给具有与传入参数相同变量名的操作，只会在函数内部命名空间创建一个相同变量名称（局部）对应的内存空间
		- 对于不可变对象和可变对象，在函数中对其的修改都不会改变其本身，而是生成一个新的对象，然后在函数中创建一个局部变量指向该对象，这一点和可变对象传参时，函数中创建新对象指向同名变量的道理是相同的。即，只要是创建新对象的操作，都会在函数内存中创建内存空间，对该新对象的操作不会影响到传参时传入的整个对象！！！

- 趁热打铁一番，再聊聊默认参数传参的问题
	- 看个很简单的代码

```python
def func(l=[]):
    print(l)
    print(id(l))
    l.append('a')
    print(id(l))

func()
func()
func()
```

- 函数传参实际上传入的是整个函数，对于上述函数，传入的对象是个空列表。并且在函数中并没有创建对象，只是对列表中的元素进行了操作，并没有创建一个新的列表，因此在在内存中该列表对象一直存在并且每一此函数运行都会往其中append一个值`a`。
- 再验证上面传值中结论二——对于创建新对象的操作，不会影响传参时传入的对象（请自行解释）

```python
def func(l=[]):
    print(l)
    print(id(l))
    l = [1, 2, 3]
    print(id(l))

func()
func()
func()
``` 


## 可变参数 *args 与 **kwargs的使用
- 注意
	1. 可变参数适合用于无法确定参数个数的时候使用。
	2. 参数列表很长的时候，可以选择使用序列代替参数列表中常常的参数，这样可以增加可读性
	3. 注意传参时实参的顺序

- 何时使用可变参数?
	- 应该从可变参数的特点去考虑。可变参数在在函数定义中代表该位置可以传入多个位置参数，并且传入的实参类型是不限制的，只要符合函数内部运行逻辑的实参都可以通过args传入。基于此，可变参数的用处大致有以下几条（包括但不限于）
		1. 当无法确定函数参数的个数时，即函数的参数个数是可变的
		2. 定义装饰器时，我们无法在定义装饰器时就知道要被装饰的func函数传入什么类型的实参
		3. 实现函数多态时
		4. 子类派生父类方法时

```python
class par:
	def foo(self, x)
		pass


def sub(par):
	def foo(self, m, *args):
		super().__init__()
		pass

```



## 使用跳转表

```python
num = input('>>>').strip()

if num == 'yes':
    print('get a yes!')
if num == 'no':
    print('get a no!')
if num == 'quit':
    print('bye!')

# ----------------------------------------------

def get_input():
    num = input('>>>').strip()
    return {
         'yes': 'get a yes!',
          'no': 'get a no!',
        'quit': 'bye!'
    }.get(num, 'get a wrong input')

res = get_input()
print(res)
```