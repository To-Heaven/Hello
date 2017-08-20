## Note 
- In Python, file names, command line arguments, and environment variables are represented using the string type
- get the system default encoding of file
	- sys.getfilesystemencoding()

```python

>>> import sys
>>> sys.getfilesystemencoding()
'utf-8'
>>> 
```


## Methods
- os.chdir(*path*)
	- Change the current working directory to path.
	- 注意：path可以是一个文件描述符（句柄）， 官方这样解释
		- This function can support specifying a file descriptor. The descriptor must refer to an opened directory, not an open file.

- os.fchdir(fd)		
	- Change the current working directory to the directory represented by the file descriptor fd
	- fd:文件描述符（句柄）， 这个句柄必须是指向一个已经打开了的文件。
		- The descriptor must refer to an opened directory, not an open file
	- 将当前脚本工作目录切换到句柄指向的目录

- os.getcwd()
	- return a string representing the current working directory
- os.getcwdb()
	- Return a bytestring representing the current working directory

```python
>>> os.getcwd()
'D:\\files'
>>> os.getcwdb()
b'D:\\files'
>>> 
```

> recursive       递归的

- os.mkdir(name, mode=0o777, *, dir_fd=None)
	- Note: mode是 零o777，第一个是数字，第二个是小写字母o
	- Create a directory named path with numeric mode mode.
		- raise FileExistsError while directory already exists

- os.makedirs(name, mode=0o777, *, exist_ok = False )
	- **注意**:这里的是makedirs，不是mkdirs

```python
>>> os.makedirs('haha\ziawang\xixi')
SyntaxError: (unicode error) 'unicodeescape' codec can't decode bytes in position 12-13: truncated \xXX escape
>>> os.makedirs(r'haha\ziawang\xixi')
>>> os.makedirs(r'haha\xixi\ziawang', exist_ok = False)        # 递归创建子目录
>>> os.makedirs(r'haha\xixi\ziawang', exist_ok = False)         # 创建完全相同的路径就会报错
Traceback (most recent call last):
  File "<pyshell#37>", line 1, in <module>
    os.makedirs(r'haha\xixi\ziawang', exist_ok = False)
  File "D:\Python36\lib\os.py", line 220, in makedirs
    mkdir(name, mode)
FileExistsError: [WinError 183] 当文件已存在时，无法创建该文件。: 'haha\\xixi\\ziawang'
>>> os.makedirs(r'haha\xixi\ziawang', exist_ok = True)       # 不会报错，也不会创建，会忽略错误
>>> 
```

> entry          记录  


- os.listdir()
	- Return a list containing the names of the entries in the directory given by path.
	- Note:**not include '.' and '..'**

```python
>>> os.getcwd()
'D:\\'
>>> os.listdir()
['$RECYCLE.BIN', '13-红蜘蛛多媒体教学软件商业版', 'CentOS-6.9-x86_64-LiveDVD.iso', 'files', 'Git', 'Go', 'Notepad++', 'PyCharm', 'Python27', 'Python36', 'screen+', 'System Volume Information', 'ubuntu-16.04.2-desktop-amd64.iso', 'Virtual Machines', 'VMware', 'wkhtmltopdf', 'work', 'XMind', '安装包',  '红蜘蛛修改频道', '视频']
>>> 
```

- os.rmdir()
	- Note:Remove (delete) the directory path, **only when the directory is empty**
		- otherwise , raise OSError

```python
>>> os.rmdir('work')
Traceback (most recent call last):
  File "<pyshell#41>", line 1, in <module>
    os.rmdir('work')
OSError: [WinError 145] 目录不是空的。: 'work'
>>> 
```

- os.removedirs()
	- 注意： 和os.rmdir()一样，当文件夹非空的时候就会报错。
		- 另外， 注意是  removedirs()  而不是   rmdirs()



- os.rename(src, dst)
	-  rename the file or a directory
	-  首先，如果dst是一个已经存在的文件夹的名字，那么不管是什么平台操作系统，都会报错
	-  如果dst是一个文件的名字，在linux和windows系统上，rename报错的机制不一样
		- linux
			-  if dst exists and is a file, it will be replaced silently if the user has permission
		- windows
			- if dst already exists, OSError will be raised even if it is a file.

```python
>>> os.rename('work', 'XMind')
Traceback (most recent call last):
  File "<pyshell#42>", line 1, in <module>
    os.rename('work', 'XMind')                              
FileExistsError: [WinError 183] 当文件已存在时，无法创建该文件。: 'work' -> 'XMind'
>>>                     # dst不能是一个已经在当前目录下存在的文件或文件夹的名字。


```

- 对于os.rename，由于在不同系统上，报错是不固定的，于是乎，python还有另一个方法
- os.replace(src, dst)
	- 用法和os.rename相同，只是报错不同
	- If dst is a directory, OSError will be raised
	- If dst exists and is a file, it will be replaced silently if the user has permission


- os.stat(path, *, dir_df=None, follow_symlinks=True)
	- Get the status of a file or a file descriptor.Return a **stat_result object.**
	- path: 
		- either string or bytes

```python
>>> os.getcwd()
'D:\\'
>>> os.chdir('files')
>>> os.listdir()
['.idea', 'blog', 'git', 'glance', 'go_workspace', 'haha', 'homework', 'img', 'little_project', 'my_module.py', 'python', 'python_practice', 'show_me_your_code', 't.py', 'The_Python_Standard_Library', 'wangzihao', '__pycache__', '捕获.PNG']
>>> f = open('t.py', 'r')
>>> os.stat(f)
Traceback (most recent call last):
  File "<pyshell#49>", line 1, in <module>
    os.stat(f)
TypeError: stat: path should be string, bytes, os.PathLike or integer, not _io.TextIOWrapper
>>> os.stat('t.py')
os.stat_result(st_mode=33206, st_ino=3940649673985678, st_dev=1443930415, st_nlink=1, st_uid=0, st_gid=0, st_size=136, st_atime=1502265076, st_mtime=1502762160, st_ctime=1502265076)
>>> 
```

- 待补充：
- os.system()
- os.popen()
- os.wait()
- os.waitpid()
- os.waitid()


## OS  variables  （not callable）

- os.name
	- return the system name

> constant         持续的

- os.pardir
	-  The constant string used by the operating system to refer to the parent directory. This is '..' for Windows and POSIX.
- os.curdir	
	- The constant string used by the operating system to refer to the current directory. This is '.' for Windows and POSIX. 

```python
>>> os.pardir
'..'
>>> os.path.abspath(os.pardir)
'D:\\'
>>> os.getcwd()
'D:\\files'
>>> os.getcwdb()
b'D:\\files'
>>> os.getcwd()
'D:\\files'
>>> os.path.abspath(os.curdir)
'D:\\files'
>>> os.curdir
'.'
>>> os.chdir(os.curdir)
>>> os.getcwd()
'D:\\files'
>>> os.chdir(os.pardir)
>>> os.getcwd()
'D:\\'
>>> os.path.abspath(os.curdir)
'D:\\'
>>> 
```
> convientionally        惯例

- os.sep
	- The character used by the operating system to separate pathname components. This is '/' for POSIX and '\\' for Windows
 
- os.linesep
	- The string used to separate (or, rather, terminate) lines on the current platform

- os.pathsep
	- The character conventionally used by the operating system to separate search path components (as in PATH), such as ':' for POSIX or ';'





























## os.walk
- 返回值是一个生成器generator
	- os.walk(top, topdown=True, oneerror=None, followlinks=False)
	- top : 要遍历目录的地址
	- topdown为真，则优先遍历top目录，否则优先遍历top的子目录（默认开启）。但是最终都会被遍历
	- oneerror需要一个callable对象，当walk异常的时候，调用
	- followlinks为真，会遍历目录下的快捷方式指向的目录
- 每次遍历的对象都是一个三元元组（root，dirs，files）
	- root 是当前正在被遍历文件夹本身的地址
	- dirs 是一个list，内容是文件夹中所有目录的名字（不包括子目录）
	- files 是一个list，内容是给文件家中所有文件，不包括子目录

