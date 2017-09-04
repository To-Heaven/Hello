# what is shelve

> shelf		格、层  
> persistent	持续的  
> recursive		递归的  
> essentially	本质的  
> arbitrary		任意的，主观的，随心所欲的  
> ordinary		普通的  

- shelf is a persistent, dictinoary-like object. 
	- the  **values ** (不是keys) in a shelf can be essentially arbitary python objects.  includes most class instances, recursive data types. and objects containing lots of shared sub-objects. 
	- the **keys** are ordinary strings

# shelve.Method

> vast                 大量的  
> consume          消耗  
> portocol		协议  
> synchronize	同步  
> entry 		记录  
> access		使用  

- shelve.open(filename,flag='c', protocol=None, writeback=False )  open a persistent dictionary 
	- the filename specified is the base filename for the underly database.
	- flag is the mode while open the file :
		- '  r  '  open existing database for reading only
		- '  w ' open existing database for writing  and reading 
		- ' c ' open database for reading and writing, creating it if it doesn't exist    (deafult, always use it as well)
		- '  n ' create a new , empty database, open for reading and writing 
			- Note:  这里的w和c不会像内置open()一样清空原文件
	- while writeback is set to True, all entries accessed are also cached in memory,and then writeen back on sync() and close().
		- if many entries are accessed , it can consume vast amounts of memory for the cache. and it can make the close opration vary slow since all accessed entries are writen back.
		- 意思是说，当创建太多的记录时，会消耗太多的内存，回写会变得很缓慢
	- Note: 
		- Do not rely on the shelf being closed automatically, always call shelve.close() explicatily
		- always use shelve as a context manager(可以利用上下文管理器来使用shelve文件)
		- shelve module is backed by the pickle .it is insecure to load a shelf from an unstruct source.like with pickle, loading a shelf can execute arbitary code（从一个不被信任的源文件获取的shelf时不安全的， 导入的shelf可以很灵活的执行一段代码）

> handy		方便的  
> mutate		改变  
> mutable		易改变的  
> explicitly		明确的  



```python
import shelve 
with shelve.open('shelve_file', 'c') as sf:
	data  = sf[key]

```

- shelve.sync()
	- write back all entries in the cache if the shelf was opened with the argument  ** writeback = True ** , also empty the cache and syncchronize the persistent dictionary on disk.
		- 使用该方法时，内存中的所有记录会被写入磁盘中的文件，并且清空缓存
	- called automatically when the shelf is closed by the shelve.close()

```python
# shelf 最初  s['key'] = {'int': 10, 'float': 9.5, 'string': 'Sample data', 'say': 'hahaha'}
-----------------
s = shelve.open('shelve_file', 'c')
s['key']['int'] = 100
s.close()

s = shelve.open('shelve_file', 'c')
data = s['key']
s.close()
print(data)
----------------------------------------------------
# 运算结果， int指向的value并没有变
{'int': 10, 'float': 9.5, 'string': 'Sample data', 'say': 'hahaha'}


--------------------------------------------
with shelve.open('shelve_file', 'c', writeback=True) as s:
    s['key'] = {'int': 10, 'float': 9.5, 'string': 'Sample data', 'say': 'hahaha'}
    s.sync()
    data = s['key']
    print(data)
------------------------------------
# 运算结果
{'int': 10, 'float': 9.5, 'string': 'Sample data', 'say': 'hahaha'}





--------------------------
s = shelve.open('shelve_file', 'c')
s['key']['int'] = 100
s.sync()					# 即使使用了该方法，可是参数write=False，所以记录不会写入文件
s.close()

s = shelve.open('shelve_file', 'c')
data = s['key']
s.close()
print(data)

----------------------
# 运算结果
{'int': 10, 'float': 9.5, 'string': 'Sample data', 'say': 'hahaha'}


---------------------------------------------------
s = shelve.open('shelve_file', 'c', writeback=True)
s['key']['int'] = 100
s.sync()
s.close()

s = shelve.open('shelve_file', 'c')
data = s['key']
s.close()
print(data)

------------------------------
# 运算结果
{'int': 100, 'float': 9.5, 'string': 'Sample data', 'say': 'hahaha'}

---------------------------------------------------------
# 在上面基础上，继续改
#  {'int': 100, 'float': 9.5, 'string': 'Sample data', 'say': 'hahaha'}

s = shelve.open('shelve_file', 'c', writeback=True)
s['key']['int'] = 1000
s.close()

s = shelve.open('shelve_file', 'c')
data = s['key']
s.close()
print(data)


----------------------------------------
# 运算结果
{'int': 1000, 'float': 9.5, 'string': 'Sample data', 'say': 'hahaha'}

```

> 当参数writeback=True的时候，使用sync()可以灵活的将记录写入内存而不用关闭文件。
> 关闭文件时自动调用sync()





- shelf.close()
	- synchronize and close the persistent dict object . operations on a closed shelf will fail with a ValueError   

```python
s = shelve.open('shelve_file', 'c', writeback=True)
s['key']['int'] = 1000
s.close()

s = shelve.open('shelve_file', 'c')
data = s['key']
s.close()
s['key']['int'] = 111111   # close() 之后再对shelf对象操作会报错

-----------------------------------------------------------------------------
Traceback (most recent call last):
  File "D:\Python36\lib\shelve.py", line 111, in __getitem__
    value = self.cache[key]
KeyError: 'key'

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "D:/files/python_practice/0807-0813/0811/practice.py", line 166, in <module>
    s['key']['int'] = 111111   # close() 之后再对shelf对象操作会报错
  File "D:\Python36\lib\shelve.py", line 113, in __getitem__
    f = BytesIO(self.dict[key.encode(self.keyencoding)])
  File "D:\Python36\lib\shelve.py", line 70, in closed
    raise ValueError('invalid operation on closed shelf')
ValueError: invalid operation on closed shelf

```




## other operations 
- del s[key]
	- delete data stored at key 
	- raise KeyError if no such key 


-  in 
	- check if the key exists

```python
s = shelve.open('shelve_file', 'c')
ret = True if 'key' in s else False
s.close()
print(ret)
-----------------------------------------
True
```

	
- shlef.keys()
	- get the keys exists ， it's slow
	- 注意 ：keys()方法得到的是一个keyview   object，类似字典的dict_keys  object  

```python
s = shelve.open('shelve_file', 'c')
l = s.keys()
print(l)
for key in l:
    print(key)
s.close()
-----------------------------------
KeysView(<shelve.DbfilenameShelf object at 0x0000027E921B9518>)
key
other_key
a
m

```

- something happend on shelf[key].append(value)
	- Note: 即使对shelf的key对应的value进行操作，只要涉及到了改变，没有把参数writeback设置为True，就无法写入

```python
>>> import shelve
>>> s = shelve.open('shelve_file', 'c')
>>> s['l'] = [1,2,3]
>>> s['l'].append(4)
>>> print(s['l'])
[1, 2, 3]
>>> s.close()
>>> s = shelve.open('shelve_file', 'c', writeback=True)
>>> print(s['l'])
[1, 2, 3]
>>> s['l'].append(4)
>>> print(s['l'])
[1, 2, 3, 4]
>>> s.close()
>>> 

```

-   the argumet writeback = True would let you just code d[key].append(value) and have it work as expected . **but it would also consume more memory and make the d.close() operation slower**

