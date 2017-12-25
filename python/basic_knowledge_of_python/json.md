# json模块

[点击查看我整理的json模块思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/json-2.png?raw=true)

## 单词 
notation		记号，标记法  
obsolete		废弃，抛弃  
interchange	交换  
inspired		启发  
literal		平实的  
useage		用法，用途  
escaped		被转义  
as-is			原样  
compact		结实，压紧  
represent	表现  
indent		缩进排版  
repeated		重复的  
call			调用  
multiple		多个  
coerce		强迫  
deserialize 	非序列化  
document	文档  

>    [json tools website ](http://www.bejson.com)

# json module
- json module always **produces str objects. not bytes objects**. 
	- file-like-object.write() must support str input


# Note

> frame   框架  

- while Try to serialize mutiple objects with repeated calls to dump() using the samp fp will result in an invalid JSON file beause json is not a framed protocol.
- 注意： 
	- **keys in key-value pairs of json are always of the type str**. 
		- when a dictionary s converted into a JSON,all the keys of the dictionary are coerced to strings.
		- if a dictionary is convert into a JSON and then back into a dictionary, the dictionary may not equal the original one.
	- that is , loads(dumps(x))  ! = x   if x has non-string keys（就是当字典的keys不是字符串的时候，被序列化之后的dict再用load或loads转换回去，就会和原来的keys不同——强制变成字符串了）

```python
note = {
    1:'get up',
    2:'have brakfirst',
    3:'go to school'
}

import  json
with open('json_dump', 'w', encoding='utf-8') as jd:
    json.dump(note, jd, indent=10)

with open('json_dump', 'r', encoding='utf-8') as jd:
    json_data = json.load(jd)
    print(json_data)

-----------------------------------------------
# json_dump
{
          "1": "get up",
          "2": "have brakfirst",
          "3": "go to school"
}

----------------------------------------
# 运算结果
{'1': 'get up', '2': 'have brakfirst', '3': 'go to school'}

``` 
 
## json.dump  and json dumps
 - json.dump(obj, fp, \*, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)
	- serialize a obj as a json formatted stream** to a fp**(file-like object), using this conversion table
	- conversion table:

![](https://raw.githubusercontent.com/ZiaWang/Hello/master/picture/json_conversion_table.jpg)


> serialize		序列化  
> formatted	有格式的  
> guarantee	担保，保证  
> character	字符  
> incoming		传入的   


- if skipkeys is True, dict keys that are not of basic type(int str float bool None) will be skipped instead of raiseing a TypeError
	- 简单说就是，当json对象中的keys不是字符串的时候，序列化该都对象会报错
	- 如果将参数skipkeys设置为True，json就会忽略这些不合json序列化规则的键值对

```python
import json

dic = {
    (1, 2, 3): 'tuple',
    666: 'int',
    'ziawang': 'string',
    True: 'get up early',
}

skipkeys_True = json.dumps(dic, skipkeys=True)
print(skipkeys_True)
skipkeys_False = json.dumps(dic, skipkeys=False)
print(skipkeys_False)
----------------------------------------------------------------------------------------
{"666": "int", "ziawang": "string", "true": "get up early"}
Traceback (most recent call last):
  File "D:/files/python_practice/0821-0827/0823/practice.py", line 25, in <module>
    skipkeys_False = json.dumps(dic, skipkeys=False)
  File "D:\Python36\lib\json\__init__.py", line 231, in dumps
    return _default_encoder.encode(obj)
  File "D:\Python36\lib\json\encoder.py", line 199, in encode
    chunks = self.iterencode(o, _one_shot=True)
  File "D:\Python36\lib\json\encoder.py", line 257, in iterencode
    return _iterencode(o, 0)
TypeError: keys must be a string

```



- if ensure_ascii is True the output is guaranteed to have all incoming non-ascii characters escaped. if ensure_ascii is False , these characters will be output as-is （就是说，这个参数如果是False，就会原样输出ASCII字符而不会对ASCII字符对象进行序列化，如果序列话的对象中包含中文字符，设置该参数为False即可在序列化后的对象中显示中文字符而不是被ASCII序列化的一串二进制）
- indent : non-negative integer or string,json array elements and object members will be pretty-printed with that indent level. 
	- indent level of 0, '', or negative will only insert newlines.
	- indent level of None, selects the most compact representation 
	- indent level of positive number , indent many spaces per level （推荐）
	- indent level of strinf like ' \t' ,  （此参数为字符串是可以是类似制表符的字符串）
- separators can be an tuple of (item_separtor , key_separtor).
	- default :  (', ', ': ')
	- the most compact json represtation: indent is None and separtors (',', ':')
	- we can specify (',',':')to eliminate whitespace.（推荐清除空格） 

```python
import json

d = {

    'b': 'banana',
    'p': 'potato',
    'o': 'orange',
    'a': 'apple',
    't': 'tomato',
}

d1 = json.dumps(d, skipkeys=True, indent=None, separators=(',', '='))
print(d1)
d2 = json.dumps(d, skipkeys=True, indent=5, separators=(',', ':'))
print(d2)
d3 = json.dumps(d, skipkeys=True, indent=5, separators=(',', ':'), sort_keys=True)
print(d3)
---------------------------------------------------------------------------------------------------
{"b"="banana","p"="potato","o"="orange","a"="apple","t"="tomato"}
{
     "b":"banana",
     "p":"potato",
     "o":"orange",
     "a":"apple",
     "t":"tomato"
}
{
     "a":"apple",
     "b":"banana",
     "o":"orange",
     "p":"potato",
     "t":"tomato"
}


```

- if sort_keys is True ,  the output of dictionaries will be sorted by key.

-  json.dumps(obj, *, skipkeys=False, ensure_ascii= True, check-circular=True, allow_nan=True, cls = None, indent=None, separators=None, default=None, sort_keys=False, **kw)
	-  serialize obj to a json formatted str using the conversion table above.


## json load and json loads
- json.load(fp, *, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)
- deserialize fp to a python object using conversion table
	- fp  file-like object contain a JSON document
	- if parse_float specified , it will be called with the string of every JSON float to be decoded.
	- equivalent to   float(num_str)
	- parse_int, if specified, will be called with the string of every JSON int to be decoded. By default, this is equivalent to int(num_str).

- json.loads(s, *, encoding=None, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)
	- s: a str, bytes or bytearray instance contain a JSON document



```python
d = {
    'name':'ziawang',
    'age':21,
    'phone':15695018792
}


import  json

data_dumps = json.dumps(d, indent=4, sort_keys=True)
print(data_dumps)

data_loads = json.loads(data_dumps)
print(data_loads)
------------------------------------------------------------------------------
# 运行结果
{
    "age": 21,
    "name": "ziawang",
    "phone": 15695018792
}
{'age': 21, 'name': 'ziawang', 'phone': 15695018792}


```

- for  parameter of encoding
	- Note: **dump(obj), obj can not be a bytes object** 

```python
>>> import json
>>> data = b'asdfgh'
>>> data_json = json.dumps(data)
Traceback (most recent call last):
  File "<pyshell#5>", line 1, in <module>
    data_json = json.dumps(data)
  File "D:\Python36\lib\json\__init__.py", line 231, in dumps
    return _default_encoder.encode(obj)
  File "D:\Python36\lib\json\encoder.py", line 199, in encode
    chunks = self.iterencode(o, _one_shot=True)
  File "D:\Python36\lib\json\encoder.py", line 257, in iterencode
    return _iterencode(o, 0)
  File "D:\Python36\lib\json\encoder.py", line 180, in default
    o.__class__.__name__)
TypeError: Object of type 'bytes' is not JSON serializable			# obj can not be a bytes object
>>> data = 'asas'
>>> data_json = json.dumps(data)
>>> data_json
'"asas"'
>>> data_json_bytes = data_json.encode('utf-8')
>>> data_json_bytes
b'"asas"'
>>> data = json.loads(data_json_bytes)
>>> data
'asas'
>>> 

```


## json.tool
> validated  使生效  

- The JSON file to be validated or pretty-printed:

```
python  -m  json.tool my_json.json


# 可以在命令行对json文件进行查看检查，用于调试
```


