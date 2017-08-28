## words
convention		习惯    
normal			正常的  
absolutized		绝对化的  
descripter		描述符  
grant			允许，准许  

## Note
- the *path* parameters  can be a string or a bytes .
	- some filenames on unix not be representable as strings on Unix
	- applications that need to support arbitrary file names on Unix should use bytes objects to represent path names

- All of these functions accept either only bytes or only string objects as their parameters. 
	- the result is an object of the **same type** , if a path or file is returned

## Methods
> slash           斜线  
> colon          冒号  
> up   to         直到  


- os.path.split(*path*)
	- Split the pathname path into a pair,** (head, tail) **
	-tail：
		- the last pathname component
	-  head：
		-  everything, lead up to 'tail'
	-  **NOTE**:
		-  path ends in a slash, tail will be empty
		-  If there is no slash in path, head will be empty
		-   If path is empty, both head and tail are empty

```python
>>> import os
>>> os.path.split('a\b\c\d')
('a\x08\\c', 'd')				# 应该使用r转义
>>> os.path.split(r'\a\b\c')
('\\a\\b', 'c')
>>> os.path.split('\a\b\c\\')
('\x07\x08\\c', '')
>>> os.path.split('asdfghj')
('', 'asdfghj')
>>> 

```



- os.path.splitdrive(*path*)
	-  Split the  path into a pair (drive, tail)
	-  drive:
		-  either a mount point or the empty string. On systems which do not use drive specifications, drive will always be the empty string
		-  On Windows, splits a pathname into drive/UNC sharepoint and relative path
	-  对于UNC 通用命名规则，等用到时候再补充



```python
>>> os.path.split(r'D:a\b\c\d')
('D:a\\b\\c', 'd')
>>> os.path.split(r'D:a\b\c\d\\')
('D:a\\b\\c\\d', '')
>>> os.path.split(r'D:abcd')
('D:', 'abcd')
>>> os.path.splitdrive(r'D:a\b\c\d')
('D:', 'a\\b\\c\\d')
>>> os.path.splitdrive('a\b\c\d')
('', 'a\x08\\c\\d')
>>> 
```

> postfix    后缀  
> period     时期，句号  


- os.path.splitext(*path*)
	- return a tuple (root, ext)
	- Split the pathname path into a pair (root, ext)
		- ext is empty or begins with a period and contains at most one period. Leading periods on the basename are ignored; splitext('.cshrc') returns ('.cshrc', '').
	- get the file-postfix

```python
>>> os.path.splitext(r'D:a\b\c\d')
('D:a\\b\\c\\d', '')
>>> os.path.splitext(r'D:a\b\c\d.py')
('D:a\\b\\c\\d', '.py')
>>> os.path.splitext(r'.py')
('.py', '')
>>> os.path.splitext(r'd.py')
('d', '.py')
```


- os.path.abspath(*path*)
	- return the absolutized version of the pathnem path
	- 即返回文件路径和文件扩展名
	- 

- os.path.basename(*path*)
	- Return the base name of pathname path	
		- a dirctory or a filename, depand on the last one
		- 最好对原始字符串操作

```python
>>> path = 'a\b\c\d\e'
>>> os.path.basename(path)
'e'
>>> path_other = 'a\b\c\d\e\n'
>>> os.path.basename(path_other)
'e\n'
>>> path_other = r'a\b\c\d\e\n'
>>> os.path.basename(path_other)
'n'
>>> path_other = 'a\b\c\d\e\l'
>>> os.path.basename(path_other)
'l'

>>> 

```


- os.path.commonpath(*paths*)
	- paths: a list for path
	- Return the longest common sub-path of each pathname in the sequence paths

```python

>>> p1 = r'a\b\c\d\e\f\g\h'
>>> p2 = r'a\b\c\f\h\d'
>>> os.path.commonpath([p1, p2])
'a\\b\\c'
>>> p3 = r'c\d\e\f\g\h'
>>> os.path.commonpath([p1, p3])
''
>>> 
```

- os.path.dirname(*path*)
	- Return the directory name of pathname path
	- 注意： 
		- 使用原始字符串
		- 返回值是除了最后一个文件夹或文件之外的其他字符串
		- 从上面可以看出：返回值是上层目录

```python
>>> path = 'D:\files\python_practice\0810'
>>> path_other = 'D:\files\python_practice\0810\test.py'
>>> os.path.dirname(path)
'D:\x0ciles'
>>> path = r'D:\files\python_practice\0810'
>>> os.path.dirname(path)
'D:\\files\\python_practice'
>>> path_other = r'D:\files\python_practice\0810\test.py'
>>> os.path.dirname(path_other)
'D:\\files\\python_practice\\0810'
>>> 
```

- os.path.exists(*path*)
	- Return True if path refers to an existing path or an open file descriptor.
	- Returns False for broken symbolic links.
	- Note: 
		-  on some platforms,  return False if permission is not granted to execute os.stat() on the requested file, even if the path physically exists.
	- 注意： 请使用原始字符串

```python
>>> os.path.exists('D:\files\python_practice\0810')
Traceback (most recent call last):
  File "<pyshell#43>", line 1, in <module>
    os.path.exists('D:\files\python_practice\0810')
  File "D:\Python36\lib\genericpath.py", line 19, in exists
    os.stat(path)
ValueError: stat: embedded null character in path				# 不是原始字符串， 遇到特殊意义的														#转义字符时报错
>>> os.path.exists(r'D:\files\python_practice\0810')
True
>>> os.path.exists(r'a\b\c\d\e\f')
False
>>> 

```

> access          进入  
> modification          修改   

- os.path.getatime(*path*)
	- Return the time of last access of path
		- the time value is a timestamp

- os.path.getmtime(*path*)
	-  Return the time of last modification of path
		- the time value is a timestamp

- os.path.getctime(*path*)
	- denpand on the paltform
		- for the unix , is the time of the last metadata change
		- for the windows,  is the creation time for path.
		-  the time value is a timestamp

```python
>>> import time
>>> time.time()
1503114829.346703
>>> os.path.getatime(r'D:\files\python_practice\0810')
1502377508.7048204
>>> os.path.getmtime(r'D:\files\python_practice\0810')
1502377508.7048204
>>> os.path.getctime(r'D:\files\python_practice\0810')
1502326860.2720635
>>> 
```


- os.getsize(*path*)
	- Return the size, in bytes, of path

```python
>>> os.path.getsize(r'D:\files\my_module.py')
112
>>> os.path.getsize(r'D:\files\xxx.py')
Traceback (most recent call last):
  File "<pyshell#56>", line 1, in <module>
    os.path.getsize(r'D:\files\xxx.py')
  File "D:\Python36\lib\genericpath.py", line 50, in getsize
    return os.stat(filename).st_size
FileNotFoundError: [WinError 2] 系统找不到指定的文件。: 'D:\\files\\xxx.py'
>>> os.path.getsize(r'D:\files')
4096
```

- os.path.isabs(*path*)
	- Return True if path is an absolute pathname

- os.path.isfile(*path*)
	- Return True if path is an existing regular file.

- os.path.isdir(*path*)
	- Return True if path is an existing directory.

```python
>>> os.path.isdir(r'D:\files\python_practice\0810')
True
>>> os.path.isdir(r'D:\files\my_module.py')
False
>>> os.path.isfile(r'D:\files\my_module.py')
True
>>> os.path.isabs(r'D:\a\b\c')
True
>>> 
``` 

> concatenation          相互关联的  
>component              零件  
> separator                 分隔符  


- os.path.join(*path*, *paths)
	- join one or more path components intelligently
	- return   the concatenation of path and any members of *paths with exactly one directory separator following each non-empty part except the last
	- If a component is an absolute path, all previous components are thrown away and joining continues from the absolute path component.

```python
>>> os.path.join(r'D:\a\b\c', r'd', r'F:\myenv\php', r'f')
'F:\\myenv\\php\\f'		#存在两个绝对路径的时，会忽略最后一个绝对路径之前的所有compoment
>>> 
```



> encountered               遇到   
	- the drive letter 驱动器号  
		- If a component contains a drive letter, all previous components are thrown away and the drive letter is reset.

```python
>>> os.path.join(r'D:', r'filedirectory', 'F:', 'my_files')
'F:my_files'             	#  注意： 不是 F:\my_files
>>> 

```

> normalize          使正常化，使标准化  
> collapsing          压扁/平  
> redundant         多余的，累赘的  
> up-level   reference       上级引用  
> manipulation             操作  
> forward slash            正斜杠  
> backward slash           反斜杠  

- os.path.normpath(*path*)
	- Normalize a pathname by collapsing redundant separators and up-level references

```python
>>> os.path.normpath(r'D:\a\b\..\c')
'D:\\a\\c'
>>> os.path.normpath(r'D:\a\b\\c\')
		 
SyntaxError: EOL while scanning string literal
>>> os.path.normpath(r'D:\a\b\\c\ ')
'D:\\a\\b\\c\\ '
>>> os.path.normpath(r'D:\a\b\\c')
'D:\\a\\b\\c'
>>> os.path.normpath(r'D:/a/b/c')
'D:\\a\\b\\c'
>>> 
```

- os.path.realpath(*path*)
- os.path.relpath(*path*)



- os.path.samefile(*path*)
	- return True , if  both pathname arguments refer to the same file or directory
		- raises an exception if an os.stat() call on either pathname fails.

- os.path.sameopenfile(fp1. fp2)
	-  Return True if the file descriptors fp1 and fp2 refer to the same file.


- os.path.samestat(stat1, stat2)
	- Return True if the stat tuples stat1 and stat2 refer to the same file
		- This function implements the underlying comparison used by samefile() and sameopenfile()




