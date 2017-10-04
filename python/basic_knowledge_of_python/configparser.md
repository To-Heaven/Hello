# configparser 模块

- [源码地址](https://github.com/python/cpython/blob/3.6/Lib/configparser.py)

## ini文件结构
- 一个ini配置文件由多个section组成，每个section中存放着配置信息。配置信息是一个个key-value键值对，这些键值对可以用等号`=`或者冒号`:`作为分隔符。


- `[section]`
	- section的名称不能重复，但是section内的key可以重复，因为即使key相同，但是属于不同的section

	
- `key=value`或`key:value`
	- key、value左右的空格会被忽略掉
		- 比如`   server address   :127.0.0.1  `等价于`server address:127.0.0.1`
	- key可以没有对应的value，这种情况下key-value的分隔符可以不写
	- value可以有多行，但是其有效性这取决于解析配置文件的模式，并且保证除第一行value之外的其他行value内容要有缩进。不然会被忽略。


	
- `#` 或 `;` 进行注释
	- 注释应在新的一行中开始，不要与配置信息在同一行



> 下面引用的是官方文档中对配置文件举得例子


```ini
[Simple Values]
key=value
spaces in keys=allowed
spaces in values=allowed as well
spaces around the delimiter = obviously
you can also use : to delimit keys from values

[All Values Are Strings]
values like this: 1000000
or this: 3.14159265359
are they treated as numbers? : no
integers, floats and booleans are held as: strings
can use the API to get converted values directly: true

[Multiline Values]
chorus: I'm a lumberjack, and I'm okay
    I sleep all night and I work all day

[No Values]
key_without_value
empty string value here =

[You can use comments]
# like this
; or this

# By default only in an empty line.
# Inline comments can be harmful because they prevent users
# from using the delimiting characters as parts of values
# That being said, this can be customized.

    [Sections Can Be Indented]
        can_values_be_as_well = True
        does_that_mean_anything_special = False
        purpose = formatting for readability
        multiline_values = are
            handled just fine as
            long as they are indented
            deeper than the first line
            of a value
        # Did I mention we can indent comments, too?
``` 


## configparser入门
### 创建一个解析器对象

> 通过解析器对象，可以操作配置文件中的配置数据，甚至创建配置文件

- configparser模块通过实例化ConfigParser得到的对象来操作配置文件
	- `my_config = configparser.ConfigParser()`

### 将配置信息保存到解析器对象
- 解析器对象的操作类似字典，但是本质上还是和字典不同的
	- 可以通过类似向字典中使用中括号`[]`插入数据的方式将配置信息保存到解析器对象中

```python
# 创建解析器对象，并将配置信息写入文件
import configparser

my_config = configparser.ConfigParser()

my_config['DEFAULT'] = {'user': 'root',
                        'password': 'pass',
                        'phone': '123-45677897',
                        'age': 20
                        }

my_config['server'] = {'user': 'ziawang',
                       'password': 'pass',
                       'age': 20,
                       'has girlfriend': 'yes',
                       'married': 'no',
                       'hobbies': ['study', 'music', 'stock'],
                       'port': 8080,
                       'address': 'http://www.ziawang.com'}

with open('my.ini', 'w') as my_configfile:
    my_config.write(my_configfile)				# 注意调用对象和被调用对象

```

```ini
# my.ini
[DEFAULT]
user = root
password = pass
phone = 123-45677897
age = 20

[server]
user = ziawang
password = pass
age = 20
has girlfriend = yes
married = no
hobbies = ['study', 'music', 'stock']
port = 8080
address = http://www.ziawang.com
```

- 注意，存储到解析器对象中的所有value，都会被转换成字符串，然后再写入到配置文件中
	- 如果你想将一个对象作为value写入配置文件中，写入配置文件中的将是该对象内存地址（引用）的字符串形式
	- 不要尝试通过在花括号外部`{}`给key赋值的形式保存配置信息，这样会抛出TypeError，因为value必须是字符串

```python
import configparser

my_config = configparser.ConfigParser()

my_config['DEFAULT'] = {'user': 'root',
                        'password': 'pass',
                        'phone': '123-45677897',
                        'age': 20
                        }

my_config['server'] = {'user': 'ziawang',
                       'password': 'pass',
                       'age': 20,
                       'has girlfriend': 'yes',
                       'married': 'no',
                       # 'hobbies': ['study', 'music', 'stock'],
                       'port': 8080,
                       'address': 'http://www.ziawang.com'}

my_config['server']['hobby'] = []									// TypeError

my_config['other'] = {'ConfigParser':configparser.ConfigParser()}	// 写入的是对象的引用

with open('my.ini', 'w') as my_configfile:
    my_config.write(my_configfile)
```

### 从配置文件中读取信息
- 读取配置文件信息同样需要创建一个解析器对象
- 对于刚创建的计息期对象，他的`sections()`方法返回的是一个空列表，因为还没有读取指定配置文件

```
# 从配置文件中读取信息
import configparser

my_config = configparser.ConfigParser()

print(my_config.sections())

my_config.read('my.ini', encoding='utf-8')

print(my_config.sections())

default_user = my_config['DEFAULT']['user']
print(default_user)

server_user_married = my_config['server']['married']
print(server_user_married)
```

### 注意
- `config.sections()`不会返回`[DEFAULT]`section，但是通过遍历可以
- 关于配置信息的数据类型
	- 配置信息以字符串的形式保存，当提取配置信息时，可能需要手动转换类型
- 不要使用`bool()`判断，应使用`config.getboolean(section, key)`
	- configparser中getboolean方法判断的value，左为True，右为False
		- `yes/no`
		- `on/off`
		- `true/false`
		- `1/0`
		- 注意，以上值全部是字符串类型，因此我们使用`bool('false')`时，得到的是True的结果
- `[DEFAULT]`section中的值优先级高于fallback回退值。某一个section对象调用get方法获取一个不存在的key时，如果这个key在`DEFAULT`section中存在，那么这个方法会返回`DEFAULT`中key对应的值，即使get方法设置了fallback回退值


### 解析器对象常用的操作
- `in`成员判断
	- 判断section
	- 判断配置信息

- `for ... in ...`遍历
	- 遍历ConfigParser对象
	- 遍历section

- 使用索引获取配置信息

```python
import configparser

my_config = configparser.ConfigParser()
my_config.read('my.ini', encoding='utf-8')

print('server' in my_config)
print('address' in my_config['server'])
print('sleep' in my_config['server']['hobbies'])

for section in my_config:
	print('-------------------------')
    print(section)
    for item in my_config[section]:
        print(item)
```


### 解析器对象的方法
#### 文件操作
- config.read(filenames, encoding=None)
	- Read and parse the list of named configuration files given by filenames
	- Non-existing files are ignored. 
	- **Return list of successfully read files**

- config.write(fileobject, space_around_delimiters=True)
	- write the configuration to the specified file object(这个file object是一个打开了的文件对象，注意使用with上下文来打开文件)
	- If space_around_delimiters is true, delimiters between keys and values are surrounded by spaces.

- config.read_file(f, filename=None)
	- Read and parse one configuration file, given as a file object
	- The filename defaults to f.name
	- it is only used in error messages


#### 查
- config.items([section,] raw=False, vars=None)
	- if section is omitted, return ItemsView object of section objects（包括`DEFAULT`section， 可以使用for循环遍历获取section对象）
	- if the section si specified, return a list of name, value pairs for the options in the section

```python
import configparser

my_config = configparser.ConfigParser()
my_config.read('my.ini', encoding='utf-8')

res_no_section = my_config.items()
print('no section:', res_no_section)
for i in res_no_section:
    print(i)

res_section = my_config.items('server')
print('section:', res_section)

# -------------result--------------
no section: ItemsView(<configparser.ConfigParser object at 0x02F710F0>)
('DEFAULT', <Section: DEFAULT>)
('server', <Section: server>)
('other', <Section: other>)
section: [('user', 'ziawang'), ('password', 'pass'), ('phone', '123-45677897'), ('age', '20'), ('has girlfriend', 'yes'), ('married', 'no'), ('hobbies', "['study', 'music', 'stock']"), ('port', '8080'), ('address', 'http://www.ziawang.com')]

```

- config.sections()
	- Return all the configuration section names, without DEFAULT

- config.has_sections(section)
	- Return True if the given section exists

- config.has_option(section, option)
	- Return True if the given option exists in the given section

- config.get(section, option, *，raw=False, vars=None, [fallback])
	- Return a string value for the named option
	- fallback
		- If the key is not found and fallback is provided, it is used as a fallback value. None can be provided as a fallback value.

- config.options(section)
	- Return list of configuration options for the named section





#### 查询转换
- config.getint(section, option, *, raw=False, vars=None[, fallback])
	- coerces the option in the specified section to an integer	

- config.getfloat(section, option, *, raw=False, vars=None[, fallback])
	- coerces the option in the specified section to a floating point number

- config.getboolean(section, option, *, raw=False, vars=None[, fallback])
	- coerces the option in the specified section to a Boolean value

#### 增
- config.read_string(string)
	- Read configuration from a given string

- config.read_dict(dictionary)
	- Read configuration from a dictionary
	- keys
		- section names
	- values
		- dictionaries with keys and values that should be present in the section
	- **Values are automatically converted to strings**

#### 删
- config.remove_option(section, option)
	- Remove the specified option from the specified section
	- section不存在，抛出NoSectionError
	- return True, if the option existed and tobe removed , otherwise retuen False

- config.remove_section(section)
	- Remove the specified section from the configuration
	- **return False if the section not existed infact**（不会报错，与remove_option不同）


#### 改
- config.set(section, option, value)
	- set the given option to the specified value if the given section exists, otherwise raise NoSectionError
	- **option and value must be strings**
		- raise TypeError if not 