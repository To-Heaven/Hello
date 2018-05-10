## 充分利用惰性运算（效率）
- 惰性运算
	- 所谓惰性运算，即仅仅在真正需要执行一段代码的时候才执行该代码，最小化计算机要做的工作
- 惰性运算的好处
	- 提高效率，避免没必要的运算
	- 可以构造一个无限长度的数据结构，节省空间

- 逻辑运算中的惰性运算
	- `if True or False`
		- 只要or左边为True，就直接返回结果，不再计算or后面的表达式的结果
	- `if False and True`
		- 只要and左边是False，就直接返回结果，不再计算and后面的表达式的结果
	- 综上
		- 对于and运算，应将False可能性高的判断内容放在and左边
		- 对于or运算，应将True可能性高的判断内容放在or的左边 
- 生成器
	- [获取生成器与迭代器知识](http://www.ziawang.com/python/basic_knowledge_of_python/iterator_generator.html)

## ++i  与  --i
- python中是支持`++i` 和 `--i` 运算的，但是仅仅是对符号的判断运算
	- 比如`--2`和`++2`两者得到`2`
	- 但是`2++`  `2--` 肯定会报错


## 连接字符串使用join而不是 + 
- 用`+`连接字符串
	- 每一次通过`+`连接两个字符串都会返回一个新的字符串对象，这就意味着在内存空间中就必须创建内存空间用于存放该对象，虽然python有垃圾处理机制，但是当用`+`连接大量的字符串时，每一次两个字符串的拼接都会申请和复制一次内存，频繁的在内存中开辟内存空间以及字符串对象的存取必然会对性能产生非常大的影响。
	
- 用`s.join(iterable)`连接字符串
	-  使用字符串的`join`方法拼接字符串的时候，解释器会首先计算并申请拼接整个字符串所需要的内存空间，然后将字符序列中的每一个元素复制到内存中去，整个过程只创建一次内存空间，大大提高了效率
	-  **join方法作用的对象必须是字符串序列**


## 生成式中要注意的地方
- 字典生成式的坑

```python
list_map = [(key, value) for key in 'abcdefg' for value in range(7)]
dict_iter = dict(list_map)
print(dict_iter)
# ------------------------------------------------------
{'a': 6, 'b': 6, 'c': 6, 'd': 6, 'e': 6, 'f': 6, 'g': 6}

# ------------------------------------------------------
dict_exp = {key:value for key in 'abcde' for value in range(10)}

print(dict_exp)
# --------------------------------------------------------
{'a': 9, 'b': 9, 'c': 9, 'd': 9, 'e': 9}
```



## with上下文管理
- with语句格式

```python
with 表达式  [as  别名]:
	代码块
```

- with语句的作用
	- 在代码块执行完毕或者遇到异常中断执行的情况下，执行清理工作，将上下文环境恢复到代码块执行之前
	- 主要用于
		- file object 的关闭
			- 文件
			- 套接字
			- stream流
			- 进程/线程/进程池/线程池
		- 锁的释放release
	
- 上下文管理的实现

```python
class TestWith:
    def __init__(self, name):
        self.name = name

    def __enter__(self):
        print('__enter__ running . . .', self.name)

    def __exit__(self, exc_type, exc_val, exc_tb):
        print('__exit__ running , , , ', self.name)


a = TestWith('ziawang')
with a :
    import time
    time.sleep(2)
``` 






## 对象的浅拷贝与深拷贝
- 浅拷贝
	- 创建一个新对象，如果被拷贝的目标对象中存在对其他对象的引用，那么浅拷贝会将这些引用也拷贝给新对象。
	- 即，拷贝的是对其他对象的引用
- 深拷贝
	- 创建一个新对象，如果被拷贝的目标对象中存在对其他对象的引用，那么深拷贝会将引用指向的对象也拷贝一份保存在新对象中。
	- 这样通过深拷贝创建的对象内就不包含其他对象的引用，对其他对象的操作就不会影响到深拷贝得到的对象

上面解释你可能看起来觉得云里雾里，其实对于深浅拷贝，用一句话总结起来就是：
- 浅拷贝拷贝的是对象的引用，而深拷贝是拷贝的是对象

以一个列表a = ['ziawang', [1, 2]]为例来解释浅拷贝中的几种情况
1. 首先浅拷贝得到b，`b = a[:]`，此时我们使用内置函数`id()`对a和b的子元素的内存地址进行比较，你就会发现a和b的元素的内存地址是相同的，这是
因为在浅拷贝的时候，b中每一个元素位置上存放的其实是a中元素对象的内存地址，当我们使用`b[0]`获取b中存储的第一个元素的时候，实际上会去获取a中
第一个位置山的元素。
2. 当我们使用`a[1].append(3)`的时候，此时你打印b就会发现b中第二个元素对应的列表中也增加了一个值`3`，这是因为b中第二个元素和a中第二个元素
指向的是同一个列表，并且列表是可变元素，当我们修改a[1]对应列表的时候，不会新创建一个新的列表[1, 2, 3]，而是在原先a[1]列表对象的基础上往里
面又放了一个值`3`，a[1]和b[1]指向的还是原来的元素
3. 当我们使用`a[0] = 'wangzihao'`的时候，此时你就会发现b[0]的值并没有改变，而a[0]的值变成了`wangzihao`，这是因为a[0]作为一个字符串是
一个不可变的对象，当`a[0] = 'wangzihao'`的时候，首先会在内存中新开辟一块内存空间存放字符串`wangzihao`，然后再让a[0]对应的内存地址修改为
存放`wangzihao`这个字符串的地址，b[0]中对应的内存地址仍然是存放`ziawang`的内存地址，并没有改变

```python
>>> a = ['ziawang', [1, 2, 3]]
>>> b = a[:]
>>> id(a), id(b)
(8649544, 8649464)
>>> id(a[0]), id(a[1])
(8713632, 8649584)
>>> id(b[0]), id(b[1])
(8713632, 8649584)
>>> a[1][:]=[]
>>> a
['ziawang', []]
>>> b
['ziawang', []]
>>> a[0] = 'wangzihao'
>>> a
['wangzihao', []]
>>> b
['ziawang', []]
>>>
```


仍然以列表 a = ['ziawang', [1, 2]]为例来解释深拷贝
- 我们使用b = copy.deepcopy(a)获的一个a的深拷贝对象b，这个时候a和b中元素的内存地址就完全不同了，这是因为当时用深拷贝的时候，解释器会在
内存空开辟出新的内存空间来存放与a中具有相同值的新对象，并将这个对象的地址交给b[0], b[1]，从而实现深拷贝
    - 你可能会发现这种情况，id(a[0])和id(b[0])对应的地址是相同的，而id(a[1])和id(b[1])对应的地址则是不同的。这里要说一下python解释器
    的小数池优化，对于一些简短常用的数据对象，python将具有相同value的多个变量指向同一个内存地址，我们可以设置一个复杂的值来避免这个优化。


#### 深浅拷贝在项目中的应用
###### 1. `seconds`开源组件
- `seconds`开源组件是我参考了Django的 admin源码之后完成的一个用来快速进行后台数据`增、删、改、查`管理的组件。在该项目中，
	1. 为了在点击列表页面的添加按钮进入到添加页面时保存列表页面的原先搜索条件，我将列表页面的搜索条件进行深拷贝
	2. 在实现组合搜索功能时，生成页面的每一个组合搜索选项对应URL，也是将当前请求的URL后的查询字符串对应的`QueryDict`进行了深拷贝，这样做的目的：每一次生成一个搜索选项时，都需要组装一次URL，如果使用浅拷贝，会破环原先列表页面发送来的请求中的搜索条件

```python
# 1. 生成搜索选项 ----------------------------------------------------------------------------

class SearchRow(object):
    """ 用于生成每一行的选项

    """

    def __init__(self, option_obj, request, data):
        self.option_obj = option_obj
        self.request = request
        self.data = data

    def __iter__(self):
        params = deepcopy(self.request.GET)
        params._mutable = True
        current_id = params.get(self.option_obj.field_name)
        current_id_list = params.getlist(self.option_obj.field_name)

        if self.option_obj.field_name in params:
            origin_list = params.pop(self.option_obj.field_name)
            url = "{0}?{1}".format(self.request.path_info, params.urlencode())
            yield mark_safe('<a href="{0}">全部</a>'.format(url))
            params.setlist(self.option_obj.field_name, origin_list)
        else:
            url = "{0}?{1}".format(self.request.path_info, params.urlencode())
            yield mark_safe('<a class="active" href={0}>全部</a>'.format(url))

        # 遍历choices列表或者quersyet，取出渲染模板需要的数据
        for obj in self.data:
            if self.option_obj.is_choices:          # 选项为二元元组组成的列表形式
                pk, text = str(obj[0]), obj[1]
            else:                                   # 选项为QuerySet
                pk = self.option_obj.val_func_name(obj) if self.option_obj.val_func_name else str(obj.pk)
                text = self.option_obj.text_func_name(obj) if self.option_obj.text_func_name else str(obj)
            if not self.option_obj.is_multi:        # 单选
                params[self.option_obj.field_name] = pk
                url = "{0}?{1}".format(self.request.path_info, params.urlencode())
                if current_id == pk:
                    yield mark_safe('<a class="active" href="{0}">{1}</a>'.format(url, text))
                else:
                    yield mark_safe('<a href="{0}">{1}</a>'.format(url, text))
            else:                                   # 选项为多选
                _params = deepcopy(params)      # ！！
                id_list = _params.getlist(self.option_obj.field_name)

                if pk in current_id_list:       # 取消勾选条件
                    id_list.remove(pk)
                    _params.setlist(self.option_obj.field_name, id_list)
                    url = "{0}?{1}".format(self.request.path_info, _params.urlencode())
                    yield mark_safe('<a class="active" href="{0}">{1}</a>'.format(url, text))
                else:
                    id_list.append(pk)
                    _params.setlist(self.option_obj.field_name, id_list)
                    url = "{0}?{1}".format(self.request.path_info, _params.urlencode())
                    yield mark_safe('<a href="{0}">{1}</a>'.format(url, text))


# 2. 添加页面重定向 --------------------------------------------------------------------------

 
```


###### 2. `rbac`开源组件
- `rbac`组件是我独自开发的`一个基于角色的权限管理系统`，其中用到深浅拷贝的地方就是`生成左侧菜单的inclusion_tag`中，每一次超链接的每一个`menu_dict`如果不使用深拷贝，菜单中每一个点击过的`<a>标签`都会被添加`active类`，这是因为每一次点击时，处理的都是同一个`menu_dict`对象，要想避免这种情况，只需要创建一个存放完全相同的数据的其他对象即可。

```python

@register.inclusion_tag(filename='rbac/menu.html')
def menu_html(request):
    """ 生成渲染菜单的数据结构并返回该数据
    Args:
        request: 请求对象
    """
    current_url = request.path_info
    menu_list = request.session.get(settings.MENU_LIST)
    menu_dict = {}
    for item in menu_list:
        if not item['group_menu']:
            menu_dict[item['id']] = item

    menu_dict = deepcopy(menu_dict)                              # 看这里！！
    for item in menu_list:
        regex_url = f'^{item["url"]}$'
        if match(pattern=regex_url, string=current_url):
            group_menu = item['group_menu']
            if group_menu:              # 当前url关联了一个组内菜单
                menu_dict[group_menu]['active'] = True
            else:                       # 当前url是组内菜单中被关联的对象(null=True)
                menu_dict[item['id']]['active'] = True  
    # 后面部分省略，有兴趣的可以去我的GitHub上看源码，那里有文档详细解释
```


## 迭代器协议
- 迭代器中只是提到了迭代协议的内容，并没有深究，这里将之前的总结一下，再继续深入
	- 可迭代对象
		- 可迭代对象即对象具备`__iter__()`方法，对象调用该方法后返回一个迭代器
		- for 循环遍历可迭代对象实际上就是使对象调用自身的`__iter__()`方法成为迭代器之后，再一个个将容器内的元素迭代
	- 迭代器协议有两段内容
		1.  对象实现了`__iter__()`方法
		2.  对象实现了`__next__()`方法
			- `__next__()`方法会返回当前指向的元素，并指向下一个元素的位置，当再次调用时，返回指向的元素，并指向再下一个元素。当当前没有再指向元素时，就抛出StopIteration异常
-  for 循环封装的功能
	-  获取容器对应的的迭代器(对象调用`__iter__()`)
	-  调用迭代器的`__next__()`方法
	-  对StopIteration抛出的错误进行处理

- **迭代器的好处**
	- 实现了访问容器的统一接口
	- 不需要在遍历之前准备好整个迭代过程中的元素
		- 可以迭代无穷个元素的数据集合
		- 未知大小的数据集合

- 因此，只要创建的对象内部实现了`__iter__()`方法和`__next__()`方法，就是一个迭代器

```python
# 创建斐波那契对象
class Fibo:
    def __init__(self):
        self.a = 0
        self.b = 1

    def __iter__(self):
        return self

    def __next__(self):
        self.a, self.b = self.b, self.a + self.b
        return self.a


for index, value in enumerate(Fibo()):
    print(value)

# =========屌丝都是这样写===========

# 浪费内存的写法
def fibo(n):
    a, b = 0, 1
    res = []

    i = 0
    while i < n:
        a, b = b, a + b
        res.append(a)
        i += 1

    return res


print(fibo(10))
```

## 深入生成器
- 使用包含yield的生成器函数创建一个生成器之后，这个生成器内部就会自动的实现`__iter__()`和`__next__()`方法，即这个生成器懈怠了迭代器协议，可以进行惰性求值
- 生成器对象`g`支持的其他方法（next, send前已论述）
	- g.close()
		- 执行`g.close()`时，生成器会停止'生产'，并抛出`StopIteration`错误
		- 当从生成器中取值结束时，解释器对其进行垃圾回收时会自动调用close()方法
	- g.throw(Exception, message)
		- 在程序逻辑中，当生成器状态满足一定条件时，可以使用`g.throw()`方法抛出一个错误
			- exception用来指定错误类型，再参数列表中是一个对象
			- message时错误信息，用字符串表示

```python
def fibo():
    a, b = 0, 1
    while 1:
        a, b = b, a + b
        yield a


gene_f = fibo()

i = 0
while 1:
    print(next(gene_f))

    if i == 10:
        # gene_f.throw(StopIteration, '10 times ')
        gene_f.close()
    i += 1
```


## 讨论一下双层循环（多层）的结束问题
- 结束双层循环有多种方式，这里列出三种方式，分别对每一种方式的“可读性”，“性能”进行分析

#### 假设
- 假设现在循环的次数非常多

#### 代码

```python
from multiprocessing import Process
import timeit

setup = 'n = 10000'


def g():
    for i in range(10000):
        for j in range(i):
            if j == i == 5000:
                return


statement1 = '''\
for i in range(n):
    for j in range(i):
        if j == i == 5000:
            break
    else:
        continue
    break
'''

statement2 = '''\
flag = False
for i in range(n):
    for j in range(i):
        if j == i == 5000:
            flag = True
            break
    if flag:
        break
'''
statement3 = '''\
try:
    for i in range(n):
        for j in range(i):
            if j == i == 5000:
                raise Exception('找到9啦')
except Exception as e:
    pass
'''

time_fuc = timeit.timeit('g()', 'from __main__ import g', number=1)
time_continue = timeit.timeit(stmt=statement1, setup=setup, number=1)
time_flag = timeit.timeit(stmt=statement2, setup=setup, number=1)
time_try = timeit.timeit(stmt=statement3, setup=setup, number=1)

print(f'time_fuc:  {time_fuc}')
print(f'time_continue: {time_continue}')
print(f'time_flag: {time_flag}')
print(f'time_try: {time_try}')

```

#### 分析
- 上面的结果看，这些方法中使用continue貌似更快些，而对于使用了额外变量flag的方式终结循环，花费时间最多
- 分析如下
	- 对于flag这种方式
		- 可读性：一般
		- 性能： 每一次循环都需要判断一次flag，处理量比较庞大的数据时，每一次判断显然都会损耗时间
	- 对于continue
		- 可读性： 较差
		- 性能： 较好
	- 对于函数
		- 可读性： 较好
		- 性能：较好
	- 对于try
		- 可读性：较好
		- 性能： 较好
		- 注意， 使用try对于多层循环时，我们可以自定义错误类型，当满足我们需要的某种类型的时候，抛出错误，获取这个值，但是使用这样方法的场景较少

#### 补充
- 可能时测试环境问题，在之后的测试中，结果变成了这样
	- 因此在生产环境中，可以按照具体测试结果进行选择

```python

```

