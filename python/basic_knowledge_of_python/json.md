www.bejson.com
## 单词 
notation		记号，标记法
obsolete		废弃，抛弃
interchange	交换
inspired		启发
literal		平实的
useage		用法，用途
serialize		序列化
formatted	有格式的
guarantee	担保，保证
character	字符
incoming		传入的
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

> json expose am API familiar to users of the standard library marshal and pickle modules

# json module
- json module always produces str objects. not bytes objects. file-like-object.write() must support str input


# Note
- while Try to serialize mutiple objects with repeated calls to dump() using the samp fp will result in am invalid JSON file beause json is not a framed protocol.
- 注意： keys in key-value pairs of json are always of the type str. when a dictionary s converted into a JSON,all the keys of the dictionary are coerced to strings.As a result of this , if a dictionary is convert into a JSON and then back into a dictionary, the dictionary msy not equal the original one.
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
 - json.dump(obj, fp, *, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)
	- serialize a obj as a json formatted stream** to a fp**(file-like object), using this conversion table
	- conversion table:
Python								JSON
dict			       						 object
list,	tuple							array
str									string
int, float, int- & float-derived Enums		number
True									true
False								false
None								null

	- if skipkeys is True, dict keys that are not of basic type(int str float bool None) will be skipped instead of raiseing a TypeError
	- if ensure_ascii is True the output is guaranteed to have all incoming non-ascii characters escaped. if ensure_ascii is False , these characters will be output as-is （就是说，这个参数如果是False，就会原样输出ASCII字符而不会对ASCII字符对象进行序列化）
	- indent, non-negative integer or string,json array elements and object members will be pretty-printed with that indent level. 
		- indent level of 0, '', or negative will only insert newlines.
		- indent level of None, selects the most compact representation 
		- indent level of positive number , indent many spaces per level （推荐）
		- indent level of strinf like ' \t' ,  （此参数为字符串是可以是类似制表符的字符串）
	- separators can be an tuple of (item_separtor , key_separtor).
		- default :  (', ', ': ')
		- the most compact json represtation: indent is None and separtors (',', ':')
		- we can specify (',',':')to eliminate whitespace.（推荐清除空格） 

```python


```


	- default argument is specified to be a function that gets called for objects that can't otherwise be serialized ????????么子意思？
	- if sort_keys is True ,  the output of dictionaries will be sorted by key.

-  json.dumps(obj, *, skipkeys=False, ensure_ascii= True, check-circular=True, allow_nan=True, cls = None, indent=None, separators=None, default=None, sort_keys=False, **kw)
	-  serialize obj to a hson formatted str using the conversion table above.


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