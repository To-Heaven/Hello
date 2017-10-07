# pickle模块
## Difference with Json
> ecosystem    生态系统     
> scope        范围    

1. serialization format
	 - json is a text serialization format 
	 - pickle is a binary serialization format, that non-Python programs may not be able to reconstruct pickled Python objects.

2. readable
	- json is hunman-readable
	- pickle is not human-readable

3. scope of application
	- json  is interoperable and widely used outside of the Python ecosystem
	- pickle just is python-specific

4. representation
	- json can only  represent a subset of the Python built-in types
		- 一个字典被json序列化的时候，默认情况下，字典的key必须是string类型
	- pickle   can represent an extremely large number of Python types 


## Module functions
- pickle.dumps(obj, protocol=None, *, fix_imports=True)
	-  return a bytes object represent the object
	-  *parameters*
		1. obj: the object to be pickled
		2. prorocol:  an integer, tells the pickler to use the given protocol
		3.  fix_imports:always be True 


- pickle.dump(obj, file, protocol=None, *, fix_imports=True)
	-  write a bytes object represent the object to the specified file
	-  file:   
		1. **must have a write() method that accepts a single bytes argument**
		2. can be a file object of an on-disk file opened for binary writing
		3. can also be an io.BytesIO instance or any other custom object that meets this interface.


- pickle.loads(bytes_object, *, fix_imports=True, encoding=”ASCII”, errors=”strict”)
	- Read a pickled object from a bytes object and return the reconstituted object specified therein.

- pickle.load(file, *, fix_imports=True, encoding=”ASCII”, errors=”strict”)
	- Read a pickled object representation from the open file object 'file' and return the reconstituted object specified therein
	- the 'file' must have two methods
		- a read() method that takes an integer argument
		-  a readline() method that requires no arguments
		-  the mode of this open file object must in a binary way  


## values can be pickled
1. None, True, and False  布尔值和None
2. integers, floating point numbers, complex numbers  各种类型数字
3. strings, bytes, bytearrays  各种类型字符串
4. tuples, lists, sets, and dictionaries containing only picklable objects  包含了pickleable 类型数据的字典、列表、集合、元组
5. functions defined at the top level of a module (using def, not lambda) 使用def定义的函数，并且函数名变量存在与全局命名空间中
6. built-in functions defined at the top level of a module. python内建的模块界别的函数
7. classes that are defined at the top level of a module 全局命名空间中创建的类
8. instance of such classes whose __dict__ or the result of calling __getstate__() is picklable 类调用__dict__或者__getstate__得到的返回值是pickleable对象的实例

-**functions (built-in and user-defined) are pickled by “fully qualified” name reference**
- **classes are pickled by named reference**
- **when class instances are pickled, their class’s code and data are not pickled along with them. Only the instance data are pickled**

> pickle unpicklable objects will raise the PicklingError exception and  unspecified number of bytes may have already been written to the underlying file


```python
import pickle
import pprint


info = {
    'name':'ziawang',
    'phone':123456789101,
    'has girl friend':True,
    'bad experience':None
}

with open('info.pickle', 'wb') as wb:
    pickle.dump(info, wb)

with open('info.pickle', 'rb') as rb:
    info_load =pickle.load(rb)
    pprint.pprint(info_load, indent=4)

--------------------------------------------------
{   'bad experience': None,
    'has girl friend': True,
    'name': 'ziawang',
    'phone': 123456789101}

Process finished with exit code 0
```

