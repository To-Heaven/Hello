# 字符串对象

## 获取字符串

#### 1. 直接使用引号创建
> type(name)可以获取对象类型


- `name = 'ziawang'`   
- 引号可以是单引号，双引号，三引号



#### 2. 实例化字符串类获取字符串
-  `class str(object='')`
-  `class str(object=b'', encoding='utf-8', errors = 'strict')   `
	-  return a string of object（**python3返回的是一个字符串，不是bytes类型的数据**）
		-  if the object is empty, return a empty string
	- arguments:
		- if neither encoding nor errors is given, str(object) return object.__str__()
		- if this object does not has the __str__method, str() fall back to returning repr(object)
	- Note: 
		- if at least  encoding or errors is given, object must be a byte-like object(bytes or bytearray）
		- if object is a byte-like object , str(object, encoding, errors)   <==> bytes.decode(encoding, errors)

```python
>>> str()
''
>>> str(b'ziawang', encoding='utf-8')
'ziawang'
>>> str(b'ziawang')
"b'ziawang'"
>>> b'ziawang'
b'ziawang'
>>> 
```

## 字符串的特点

1. **字符串是序列，是可迭代的对象**
2. **字符串中的每一个字符都对应一个索引，可以通过索引来获取字符串中的字符或者切片来获取一个子字符串**
3. **字符串不可以被更改，因为他们是不可变的**


```python
>>> b = 'qa'
>>> b[0] = 'a'
Traceback (most recent call last):
  File "<pyshell#18>", line 1, in <module>
    b[0] = 'a'
TypeError: 'str' object does not support item assignment
>>> 
```

## 字符串的应用
1. 最最常用到的，就是存放数据，比如从文件中读取的字符串
2. 配置文件中，特别是在Web项目比如Django中，通常以字符串来保存配置项，并通过`反射`来调用配置。
	- 比如中间件配置、已注册应用配置等等

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'trial.apps.TrialConfig',
    'curd.apps.CurdConfig',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = 'seconds.urls'

```

## 字符串的常用操作 common operations


#### 1. f 字符串
- python3.6新操作`f'...{value}'` 拼接字符串
	- 除了简单的拼接字符串的功能外，还可以在大括号中传入表达式比如可以传入`"zia" + "wang"`这样也能输出相同结果
	- 表达式内可以直接是数字、列表，对数字可以在大括号内进行运算并输出
	- 在平常使用时，**为了保证可阅读性，可以用变量指向该表达式，传入变量即可**

```python
>>> name = 'ziawang'
>>> str_say = f'my name is {name}'
>>> str_say
'my name is ziawang'
>>> str_num = f'result is {1_5856_1230 ** 2}'
>>> str_num
'result is 25141663659112900'
>>> str_list = f'result is {[1, 2] * 2}'
>>> str_list
'result is [1, 2, 1, 2]'
>>> def f():
...     return 'haha'
...
>>> f'aaaa:{f()}'
'aaaa:haha'
>>>
```

#### 2. 字符串的连接
- 一个字符串是一个整体，它是不可变的类型，因此**字符串连接总是返回一个新字符串**
- 拼接的集中姿势
	- 可以用 ` + ` 操作符连接（粘到一起，中间不会有空格）
	- 可以用逗号隔开，连接后会在中间存在空格
	- 如果没有加号，相邻的两个字符串会自动的连接在一起，但是这种情况不适用于一个字符串加上一个指向字符串的变量。这种情况下只能使用加号来连接代表字符串的变量和字符串


``` python
>>> s1 = 'ziawang'
>>> s2 = 'haha'
>>> s1+s2
'ziawanghaha'
>>> 'ziawang', 'haha'
('ziawang', 'haha')
>>> print('ziawang','haha')
ziawang haha
>>> 'ziawang''haha'
'ziawanghaha'
>>> 
```

###### 注意
- `M += N` 与 `M = M + N`是不同的! 
	- 对于不可变对象来说，这两种方法都会创建一个新的对象
	- 对于可变对象来说，前者不会创建一个新对象，而后者会创建一个新的对象。
	- 直接点说，不管对象是可变也好，不可变也罢，`M = M + N`这种方式总会占用更多的内存


```python
>>> a                             # 不可变对象 
'hello ziawang'
>>> id(a)
57722104
>>> a = a + ' ziawang'
>>> a
'hello ziawang ziawang'
>>> id(a)
57692672
>>> a = []                        # 可变对象
>>> id(a)
57674808
>>> a += [1]
>>> a
[1]
>>> id(a)
57674808
>>> a = a+ [2]
>>> a
[1, 2]
>>> id(a)
57721480
>>>
```

#### 3. 字符串乘法
- 可以用 *   来重复

``` python 
>>> 3*'ziawang' + ',haha'
ziawangziawangziawang,haha
```

#### 4. 原始字符串
- 原始字符串 `r`  (raw-string)
	- ` r'prefix that disables most escape sequence processing'`
	- 原始字符串中的`/`只能看作普通的反斜杠而不是转义字符

```python
>>> print('asdasdas\tdasd')
asdasdas	dasd
>>> print(r'asdasdas\tdasd')
asdasdas\tdasd
>>> 
```

###### 项目应用
1. Django2.0版本中的`re_path`和2.0以下版本的`url`在实现路由映射的时候都大量用到了原始字符串
2. 爬虫时，对正则表达式字符串使用原始字符串可以避免n个莫名其妙的坑

#### 5. 字符串的切片
- 过大的索引值（下标值大于实际长度）会被字符串实际长度代替
- 当从下边界指向上边界方向与步长方向相反的时候，就会返回空字符串

```python
>>> a = 'qwertyu'
>>> a[0:200]
'qwertyu'
>>> a[200:6]
''
>>> a[-1:-2]
''
>>> a[-5:0]
''
>>> a[-5:-1]
'erty'
>>> a [0:]
'qwertyu'
>>> a [:0]
''
>>> a[:-1]
'qwerty'
>>> a[0] == a[0:1]
True
>>> 
```


#### 6. 使用索引
- 字符串也可以用索引来获取组成字符串中的元素
- 以s = 'abcde'为例

```python
>>> s = 'abcde'
>>> s[-1]
'e'
>>> s[-5]
'a'
>>> s[-0]
'a'
>>> s[0]
'a'
>>> 
```

- 字符串的比较是按照字符位置一次比较。   'A'<'Z'<'a'<'z'

```python
>>> s1 = 'a'
>>> s2 = 'asdfff'
>>> s1 == s2
False 
>>> s1 > s2
False
>>> s1<s2
True
>>> s1 = 'abc'
>>> s2 = 'ab'
>>> s1 > s2
True
>>> 

```


#### 7. 成员判断
- 使用`in`可以判断一个字符串是否是另一个字符串的子集
	- 参与运算的数据必须是字符串类型，否则会报错

```python
>>> s
'0123456789'
>>> 0 in s
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'in <string>' requires string as left operand, not int
>>> '0' in s
True
>>> '123' in s
True
>>> '134' in s
False
>>>
```	

#### 8. 占位符%s
- 用来拼接字符串，还有两个经常用到的是字符串的`format`和`join`方法
	- `"%s, %s" % ("hello", "ziawang")`
- 这个操作经常会用到，几乎每一个项目中，都会用到

- 字符串中的占位符较多时会影响阅读性，此时应该使用format

###### 应用——拼接字符串
1. Django的源码中的大量使用到了占位符来拼接
2. 在我们自定义错误类型的时候，错误信息中的一些内容应该是变化的，我们可以使用占位符来代替

```python
# 比如视图函数不返回响应值的时候，会抛出的错误
if response is None:
    raise ValueError(
        "%s.process_template_response didn't return an "
        "HttpResponse object. It returned None instead."
        % (middleware_method.__self__.__class__.__name__)
    )
```

3. 在开发过程中，我们通常需要为一些动态变化着的数据进行拼接。比如在开发`seconds`组件的过程中，为每一个模型生成分别对应`增、删、改、查`路径，以及为这个路径创建一个反响解析时用到的name，也是用的占位符，这是在一个字符串中的占位符数量不是很多的情况下，如果占位符太多，我们应使用`format`来提高阅读性

```python
    def get_urls(self):
        """ 生成路径与视图函数映射关系的列表，并扩展该列表
        Return:
            返回存放路由关系的列表
        """

        # 生成增、删、改、查基本映射关系
        app_model = self.get_app_model()
        urlpatterns = [
            re_path(r'^$', self.add_request_decorator(self.show_view), name='%s_%s_show' % app_model),
            re_path(r'^add/$', self.add_request_decorator(self.add_view), name='%s_%s_add' % app_model),
            re_path(r'^(\d+)/delete/$', self.add_request_decorator(self.delete_view), name='%s_%s_delete' % app_model),
            re_path(r'^(\d+)/change/$', self.add_request_decorator(self.change_view), name='%s_%s_change' % app_model),
        ]

    def get_delete_url(self, nid):
        """ 获取删除记录对应的路径
        Args:
            nid: 该记录的id
        Return:
            字符串形式的路径
        """

        alias = 'curd:%s_%s_delete' % self.get_app_model()
        return reverse(alias, args=(nid, ))

    def get_change_url(self, nid):
        """ 获取编辑记录对应的url
        Args:
            nid: 该记录的id
        Return:
            字符串形式的路径
        """

        alias = 'curd:%s_%s_change' % self.get_app_model()
        return reverse(alias, args=(nid, ))

    def get_show_url(self):
        """ 获取列表页面的url
        Return:
            字符串形式的路径
        """

        alias = 'curd:%s_%s_show' % self.get_app_model()
        return reverse(alias)

    def get_add_url(self):
        """ 获取增加记录对应的url
        Return:
            字符串形式的路径
        """

        alias = 'curd:%s_%s_add' % self.get_app_model()
        return reverse(alias)
```

## 字符串的方法methods

#### 查询


- str.endswith(sub[start[end]])判断字符串的结束位置的字符是否是指定值，也可以用start和end限定判断范围

```python
>>> s = 'qwertqwerqwer'
>>> len(s)
13
>>> s.endswith('e',0,12)
True
```


- str.find(sub[start[end]]) 从字符串的左边开始找
	- Return the lowest index in the string where substring sub is found within the slice  s[start:end].Optional arguments start and end are inter preted as in slice notation. **Return -1 if sub is not found.**
	- 如果被查询的子字符串不存在，会返回`-1`

```python
>>> s
'0123456789'
>>> s.find('23')
2
>>> s.find('23', 2)
2
>>> s.find('23', 3)
-1
>>> s.find('24', 0)
-1
>>>
```

- str.rfind(sub[start[end]])  从字符串的右边开始找
	- **注意**
		- 两种方法得到的结果是相同的，即该字符sub所在的索引是固定不变的（但是当一个字符串中存在了重复的字符的时候得到的结果就是不一样的）
		- 可以用`find`和`rfind`来判断字符串重是否存在重复字符串，但是建议用`count`方法

```python
>>> s = 'asdwqfcqfrwef'
>>> s.find('f')
5
>>> len(s)
13
>>> s.find('f',6)
8
>>> s.find('p')                   #返回 -1
-1
>>> s.rfind('c')
6
>>> s.rfind('c', -4, -6)
-1
>>> 
```


- str.index(sub[start[end]])  和find一样，但是当没有找到substring的时候会爆出ValueError错误
- str.rindex(sub[start[]end])  从右边开始，也会爆出Value错误
- str.count(sub[start[end]])返回字符串中某字符串出现的次数，可以用start和end来限制统计范围

```python
>>> s = 'qwertqwerqwer'
>>> len(s)
13
>>> s.count('q')
3
>>> s.count('q',1)
2
>>> s.count('q',1,8)
1
>>> 
```

- max(str) 
- min(str)
-  返回字符串中最大/小的字母

#### 字符串切割
- str.partition(string)  返回一个三元元组。该方法将str的副本进行分割，第一个元素是分隔符左边的子字符串，第二个为分隔符本身，第三个为分隔符右边的子字符串

```python
>>> s = 'ziawangisfrom Bozhou'
>>> s.partition('is')
('ziawang', 'is', 'from Bozhou')
>>> 
```

- str.splitlines(keepends=False)
	-  return a list of the lines in the string , breaking at the boundaries .
	-  line breaks are not included in the resulting list unless keepends is given and True
	-  按照字符串中的换行符来切割字符串，`keepends`参数可以用来保留换行符
	-  `keepends=False`的时候，两个相邻的换行符会切出来一个空字符串`''`
	
```python
>>> s = '12\n3\n\n456\r\n789\f4567\v'
>>> s
'12\n3\n\n456\r\n789\x0c4567\x0b'
>>> s.splitlines()
['12', '3', '', '456', '789', '4567']
>>> s.splitlines(keepends = True)
['12\n', '3\n', '\n', '456\r\n', '789\x0c', '4567\x0b']
>>> 

``` 

- string.split(s = '',  num = string.count(s))  通过分隔符  s 对字符串进行切片，参数num为切割的次数
	- 从左边开始切分字符串
	- str默认为所有的空字符，包括空格，换行\n，制表符\t
	- num分割次数
	- **注意**
		- 要将字符串以空格切分时，如果没有指定split的参数为`' '`，那么会将连续的多个空格作为一个空格切分。如果指定了参数为一个`' '`空格，那么就会按照指定的字符串切分原字符串，并且在处理连续的空格时会产生空字符串`''`


```python
>>> s  = 'haha \t haha \thaha \thaha'
>>> s.split(' ', 2)
['haha', '\t', 'haha \thaha \thaha']
>>> s.split()
['haha', 'haha', 'haha', 'haha']
>>> s = 'asd asd  asd asd sss ss'
>>> s.aplit()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'str' object has no attribute 'aplit'
>>> s.split()
['asd', 'asd', 'asd', 'asd', 'sss', 'ss']			# 将多个连续的空格当作一个空格
>>> s.split(' ')
['asd', 'asd', '', 'asd', 'asd', 'sss', 'ss']		# 产生了空字符串
>>>
```

- 应用场景：获取字符串中指定的子字符串（可以对用户输入的字符串进行格式化，每隔一定长度插入一个特定字符，这样方便之后用split切分成列表）

```python
while True :
	cmd = input('>> : ').strip()
	if len(cmd) == 0   :    continue
	cmd_new = cmd.split()
	print('命令shi:%s, 命令的参数是%s'%(cmd_new[0], cmd_new[1]))	
```

- rsplit(s = '',  num = string.count(s))
	- 从右边开始切分字符串
	- 其他用法和split相同

###### split与rsplit在项目中的应用
- 在`问卷调查`项目中，问卷中每一类问题的对应不同的答案类型，总共`单选、打分、建议`三种问题分别对应的答案在数据库表中的字段名为`option, val, content`，要针对不同类型的问题生成不同form组件的字段在问卷页面上显示，同时考虑到了保存数据到数据库的情况，所以将字段名与问题的id拼接在一起，当提交数据时直接使用切分字符串，既可以得到问题id，又可以得到问题类型。
- 为什么在项目中用的时`rsplit`而不是`split`？
	- option字段在答案表中是一个外键foreignkey，关联的是每一个问题的选项组成的表，因此在创建一条记录的时候，需要`create`函数中要使用的是`option_id=xxx`，对应的拼接的字符串就是类似于这种`option_id_1`，如果我们使用split切分字符串提取问题类型与问题id的时候，就会切成`option`和`id_1`，这样创建记录很定会报错，所以使用`rsplit("_", 1)`就可以完美解决这个问题

```python
    for que in question_list:
        if que.tp == 1:
            field_dict['val_%s' % que.id] = fields.ChoiceField(
                label=que.caption,
                error_messages={'required':'必填'},
                widget=widgets.RadioSelect,
                choices=[(i, i) for i in range(1, 11)]
            )
        elif que.tp == 2:
            field_dict['option_id_%s' % que.id] = fields.ChoiceField(
                label=que.caption,
                widget=widgets.RadioSelect,
                choices=models.Option.objects.filter(
                    qs_id=que.id).values_list('id', 'name'))
        else:
            field_dict['content_%s' % que.id] = fields.CharField(
                label=que.caption, widget=widgets.Textarea, validators=[func, ])

    MyTestForm = type("MyTestForm", (Form,), field_dict)

    if request.method == 'GET':
        form = MyTestForm()
        return render(request, 'score.html', {'question_list': question_list, 'form': form})
    else:
        # 15字验证
        # 不允许为空
        form = MyTestForm(request.POST)
        if form.is_valid():
            objs = []
            for key,v in form.cleaned_data.items():		## {'option_id_2': '3', 'content_9': 'sfasdf', 'val_10': '13'}
                k,qid = key.rsplit('_',1)
                answer_dict = {'stu_id':student_id,'question_id':qid,k:v}
                objs.append(models.Answer(**answer_dict))
            models.Answer.objects.bulk_create(objs)
            return HttpResponse('感谢您的参与!!!')

        return render(request, 'score.html', {'question_list': question_list, 'form': form})
```

### 字符串判断

- str.islower()检测字符串是否由小写字母组成
- str.isupper()检测字符串中所有字母是否全部都是大写
- str.isalpha()检测字符串是否是只由字母组成
- - str.isalnum()检测字符串是否是由字母和数字组成
- str.istitle() 检测字符串中所有单词的首字母是否全部都大写
- str.isspace()检测字符串是否只由空格组成
- str.isdigit()  检测字符串是否是只由数字组成。是则返回True
- str.isnumeric()检测字符串是否只由数字组成，但是只针对Unicode对象


- str.isdigit()判断一个字符串里面是否全是由数字组成，在python3中，isdigit只能识别str，byte和unicode，无法进行字符串中中文数字和罗马数字的判断
- str.isdecimal()在python3中只可以对str和unicode进行识别，不能识别byte，中文数字和罗马数字
- str.isnumberic()可以判断str，unicode，中文，罗马数字

> bytes 没有 isdecimal(), isnumber()方法


- **应该选择str.isdigit()来进行数值判断，因为它适用于大部分场景**

```python
AGE = 56
while True :
	age = input('please input the age :').strip()
	if len(age) == 0:   continue
	if age.isdigit() :
		# ...balabala...
```


### 返回新字符串

- str.maketrans(intab, outtab)  创建子字符隐射的转换表，进行字符转换，源字符串不会改变
	- 这个方法与replace的最大区别在于，他可以批量的设置要替换的映射关系
	- 返回值是一个映射表，用来作为`translate()`的参数
	- intab 字符串中要被替代的字符组成的字符串
	- outtab对应intab中字符的字符串
	- **注意：python2.x**
		- 使用str.maketrans()方法必须要先从模块string中调用maketrans方法
- str.translate(table)  根据参数table提供的表来转换字符串中的字符
- 翻译表table通过maketrans()方法转换而来

```python
s = 'asdasdasdasdasdasd'

d = {
    'a': 'A',
    's': 'S',
    'd': 'D'
}
trans_table = s.maketrans(d)

print(s.translate(trans_table))
```

- str.join(iterable) 将序列sequence中的元素用str连接起来，返回一个生成新的字符串。但是要注意的是，该**序列的元素应该是字符串，如果是数字就会报错**

> 官方文档
> join(...)
> S.join(iterable) -> str
> Return a string which is the concatenation of the strings in the  iterable.  The separator between elements is S.

```python
>>> s = '-'
>>> phone_number = (156,9501,8792)
>>> s.join(phone_number)
Traceback (most recent call last):
  File "<pyshell#4>", line 1, in <module>
    str.join(phone_number)
TypeError: sequence item 0: expected str instance, int found
>>> p = ('156','9501','8792')
>>> s.join(p)
'156-9501-8792'
>>> 
```

- str.join()的问题在于它仅仅适用于字符串。这意味着通常需要一些转换

```python
>>> row = ['haha', 1, 2 ,3 , 4]
>>> row = ['haha', 1, 2, 3, 4]
>>> ', '.join(row)
Traceback (most recent call last):
  File "<pyshell#2>", line 1, in <module>
    ', '.join(row)
TypeError: sequence item 1: expected str instance, int found
>>> ', '.join(str(i) for i in row)            #最终还是要处理成字符串
'haha, 1, 2, 3, 4'
>>> 
```

- **可以用print函数这样写**

```python
>>> row = ['haha', 1, 2, 3, 4]
>>> print(*row, sep=', ')
haha, 1, 2, 3, 4
>>> 

```


- str.lstrip([char]) 截掉字符串左边指定的字符或字符串中存在的字符（默认是空格, \n, \t）
- str.rstrip([char]) 截取掉字符串右边指定的字符，或字符串中存在的字符（默认是空格,\n, \t）
- str.strip([char]) 截掉字符串首尾指定的字符或字符串中存在的字符（默认是空格, \n, \t），

```python
>>> s = '   ahha  haha   '
>>> s_new = s.strip(' ')
>>> s_new
'ahha  haha'
>>> s_new = s.strip('a')
>>> s_new
'   ahha  haha   '
>>> s_new = s.lstrip(' ')
>>> s_new
'ahha  haha   '
>>> s_new = s.rstrip()
>>> s_new
'   ahha  haha'
>>> s
'   ahha  haha   '
>>> str = 'ziawang'
>>> str = '   zi  wang  haha a '
>>> str.strip('a')
'   zi  wang  haha a '
>>> str.strip('a ')
'zi  wang  hah'
>>> str.strip('a z')
'i  wang  hah'
>>> str.strip(' ahzi')
'wang'
>>> 
```

- **用处**
	- 用于对用户输入的信息进行格式化（去除无用的内容）

```python
name = input().strip(' ')   # 去除多余的空格
password  = input().strip(' ')
```


-  str.casefold()返回一个字符串，全部小写（支持多种编码，lower只支持ASCII编码，即支队A—Z有效）
> **原str并未被修改**

```python
>>> str.casefold()
'ziawang'
>>> str
'ZiaWang'
>>> 
```

- str.swapcase()反转字符串中字母的大小写
	- Return a copy of S with uppercase characters converted to lowercase and vice versa.

- str.center(width[fillchar])返回一个指定width长度的字符串，并将原字符放在中间，其他位置用fillchar填充

```python
>>> str = 'ZiaWang'
>>> str.center(20, '-')
'------ZiaWang-------'
>>> str
'ZiaWang'
>>> 
```

> **原str并未被修改**

- str.capitalize()返回一个字符串，首字母大写，其他的字母小写
	- Return a capitalized version of S, i.e. make the first character have upper case and the rest lower case.

```python
>>> str  = 'ziawang'
>>> str.capitalize
<built-in method capitalize of str object at 0x00000155690D4DF8>
>>> str.capitalize()
'Ziawang'
```

- str.title()  返回一个标题化的字符串，所有的单词以大写开始，其余字母均变成小写

```python
>>> s = 'ziawang   is  23 years old'
>>> s.title()
'Ziawang Is 23 Years Old '
>>> 
```




- str.expandtabs(tabsize=8) 返回一个字符串的副本，并选择指定的tabsize对字符串进行扩展
	- 当tabsize<len(str)的时候，tabsize为从\t位置开始将要空出的空格数
	- 当len(str)<tabsize的时候，从\t位置要空出的空格数为tabsize-len(str)

```python
>>> str = '1234\t1234\t1234'
>>> str
'1234\t1234\t1234'
>>> print(str)
1234	1234	1234
>>> str.expandtabs(10)
'1234      1234      1234'
>>> str.expandtabs(0)
'123412341234'
>>> str.expandtabs(2)
'1234  1234  1234'
>>> str.expandtabs(7)
'1234   1234   1234'
>>> str = '12345'
>>> str.expandtabs(0)
'12345'
>>> str = '12345\t12345\t12345'
>>> str.expandtabs(0)
'123451234512345'
>>> str.expandtabs(5)
'12345     12345     12345'
>>> str.expandtabs(9)
'12345    12345    12345'
>>> str.expandtabs(11)
'12345      12345      12345'
>>> 
``` 




- str.format(*args, **kwargs) 格式化字符串，并返回格式化处理后的字符串。
	- 在定制类时的操作

```python
class Demo:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return 'Demo({self.x}, {self.y})'.format(self=self)	


d = Demo(1, 2)
print(d)

------------------------------------------------------------

Demo(1, 2)

```


- s.format常用操作
	- 在`{}`中不指定元素索引或key的时候，如果左侧`{}`数量多余args或kwargs中元素数量，就会报错，但是如果args或kwargs中元素数量多余`{}`数量，就不会报错

```python
args = ['ziawang', 23, 'Beijing', 'stock and python']

s1 = "my name is {}, I'm {} years old this year, I'm living in {} now. I love {}".format(*args)    	
print(s1)
# 通过列表索引来设置参数
s2 = "my name is {0[0]}, I'm {0[1]} years old this year, I'm living in {0[2]} now. I love {0[3]}".format(args)
print(s2)
# 可以重复使用且位置任意指定
s3 = "I'm {0} ,I love {3}, I'm {0}".format(*args)
print(s3)
kwargs = {'name':'ziawang', 'age':23, 'city':'Beijing', 'favorite':'stock , python'}
# name、age等key在传入的时候是不需要用双引号的。
s4 = "my name is {name}, I'm {age} years old this year, I'm living in {city} now. I love {favorite}".format(**kwargs)
print(s4)
```

- format与格式化占位符区别

```python

# name = input('name：')
# age = input('age: ')
# hobby = input('hobby: ')
# print('''\
# ------%s  info----------
#     name : %s
#     age : %s
#     hobby : %s
# ------------------------
# '''%(name, name, age, hobby))

#  但是如果hobby有好几个呢？



name = input('name : ')
age = input('age :')
hobby = []
times = int(input('你有几个爱好？'))
while True:
    if times == 0:
        break
    hobby_l = input('请输入你的爱好： ')
    hobby.append(hobby_l)
    times -= 1
info = dict([('name', name),('age', age), ('hobby', hobby)])

s = '''
-------------------------
name : {name}
age : {age}
hobby : {hobby}
-------------------------
'''.format(**info)
print(s)
```

> 输出结果

```python
my name is ziawang, I'm 23 years old this year, I'm living in Beijing now. I love stock and python
my name is ziawang, I'm 23 years old this year, I'm living in Beijing now. I love stock and python
I'm ziawang ,I love stock and python, I'm ziawang
my name is ziawang, I'm 23 years old this year, I'm living in Beijing now. I love stock , python
```

- format 可以实现大部分字符串方法
	- {:>width}右边显示字符串
	- {:<width}左边显示字符串
	- {:^width}中间显示字符串
	- {:filed_char^width} 中间显示字符串，并且用fillled_char填充

```python
>>> '{:^30}'.format('centered')
'           centered           '
>>> '{:<30}'.format('left')
'left                          '
>>> '{:>30}'.format('right')
'                         right'
>>> '{:^30}'.format('center')
'            center            '
>>> '{:-^30}'.format('center_filled')
'--------center_filled---------'
>>> 

```

- format格式化输出时间
	- Note: {: formatter}前面要有冒号

```python
>>> format_time = datetime.datetime(2017, 8, 18, 20, 5, 20)
>>> 'format_time     ==》  {:%Y-%m-%d  %H:%M:%S}'.format(format_time)
'format_time     ==》  2017-08-18  20:05:20'
>>> 

```





- str.lower()返回一个全是小写字母的字符串，源字符串未改变
- str.upper()返回一个全是大写字母的字符串，源字符串未改变

```python
>>> s = 'ASDASD'
>>> s.lower()
'asdasd'
>>> s
'ASDASD'
>>> s = s.lower()
>>> s
'asdasd'
>>> s.upper()
'ASDASD'
>>> s
'asdasd'
>>> 
```

- str.ljust(width[fillchar])  返回一个源字符串左对齐，并且使用指定字符（默认空格）填充至长度width的新字符串
- str.rjust(width[fillchar]) 返回一个源字符串右对齐，并且使用指定字符（默认空格）填充至长度为width的新字符串

```python
>>> str = '12345'
>>> s = str.ljust(10, '*')
>>> s
'12345*****'
>>> s = str.rjust(10, '+')
>>> s
'+++++12345'
>>> 

```


- str.zfill(width)  返回指定长度的字符串，字符串右对齐，前面填充0

```python
>>> s = 'haha'
>>> s.zfill(10)
'000000haha'
>>> s
'haha'
>>> 

```




## python字符串连接的三种方法以及其效率、适用场景
- 方法一：直接用加号连接

```python
info  = 'ziawang'  +  'is  23  years old  '  +  'living  in  Beijing'
```

- 方法二：str.join(iterable)方法

```python
info  = ['ziawang',  'is  23  years old  ',  'living  in  Beijing']
new_info = ''.join(info) 


name = 'haha'
age = '23'
city = 'Beijing'
l = [('%s Info'%(name)).center(30, '*'), 'age:  '+age, 'city:  '+city, 'end'.center(30, '*')]
print('\n'.join(l))
```

- 方法三： 替换

```python
new_info = '%s%s%s'%('ziawang', 'is  23  years old  ', 'living  in  Beijing')

info = ['ziawang', 'is  23  years old  ', 'living  in  Beijing']
  #  new_info = '%s'*len(info)%(*info)  这句语法是错误的语法
```

-说明:
	1. 方法一：
		- 简单直接，当连续进行较多的字符串进行连接的时候效率会低于join()
		- 字符串是不可变类型，当用加号连接两个字符串的时候会生成一个新的字符串，生成新的字符串就需要重新申请内存，当连续相加的字符串太多的时候效率会差

	2. 方法二：对多个字符进行连接时候效率高，只会有一次内存的申请。而且对list的字符进行连接时候，这个方法必须是首选
	3. 方法三：要处理的字符串个数较多的时候  %s 不太方便，（此处留着等方法） ，



##  将字符串中的字母转换成字符存储到列表中

- 下面是笨方法

```python
>>> s = '123456789'
>>> l = [j for i,j in dict(enumerate(s)).items()]
>>> l
['1', '2', '3', '4', '5', '6', '7', '8', '9']
>>> 
```
- 其实直接用list()函数对tr进行处理即可
	- list(iterable) -> new list initialized from iterable's items

```python
>>> s = 'ziawang'
>>> l = list(s)
>>> l
['z', 'i', 'a', 'w', 'a', 'n', 'g']
>>> 

```

> perform            执行
> substitution    替换
> precedence      优先权


# 模板字符串对象Template string
## 模板字符串

- **template string support $-based substitutions**
	- $$ is an escape, it is replaced with a single $ 
	- $identifier  names a substitution palceholder matching a mapping key of 'identifier'
	- ${identifier} is equivalent to $identifier. 
		- required when vaild identifier characters follow the placeholder but are not the part of the placeholder, such as ' ${noun}ification'

## 模板字符串的方法

- string.Template(template_string)
	- create a template object

- template_string.substitution(mapping, **kwargs)
	- perform the template substitution, return a new string.
	- mapping : 
		- dictionary-like object with keys that match the placeholders in the template
	- **Note:**
		- keywords can be provided, but the keywords must in the placeholders
		- while both mapping and keywords exists. placeholders from keywords take precedence

- template_string.safe_substitution(mapping, *kwargs)
	-   if placeholders are missing from mapping and kwds, instead of raising a KeyError exception, the original placeholder will appear in the resulting string intact

```python
>>> template_string = Template('$name has a $thing')
>>> template_string.substitute(name = 'ziawang', thing = 'book')
'ziawang has a book'
>>> d = {'name':'ziawang', 'thing':'book'}
>>> template_string.substitute(d)
'ziawang has a book'
>>> template_string.substitute(name = 'ziawang')
Traceback (most recent call last):
  File "<pyshell#16>", line 1, in <module>
    template_string.substitute(name = 'ziawang')
  File "D:\Python36\lib\string.py", line 126, in substitute
    return self.pattern.sub(convert, self.template)
  File "D:\Python36\lib\string.py", line 119, in convert
    return str(mapping[named])
KeyError: 'thing'
>>> template_string.safe_substitute(name = 'ziawang')
'ziawang has a $thing'
>>> 

```























