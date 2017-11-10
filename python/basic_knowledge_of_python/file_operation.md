# 操作文件

## 操作系统、应用程序与文件
- 应用程序产生的数据保存到硬盘中才能永久保存，但应用程序想要操作硬盘必须通过操作系统。操作系统为应用程序提供了处理文件的接口，并且提供了文件这个抽象的对象。应用程序通过操作系统操作文件就能实现对硬盘中数据的操作。


- 打开一个文件包含两部分资源，分别是操作系统级别打开的文件，另一个是应用程序级别变量（这个变量通常是句柄或文件描述符）

##### 文件处理流程
1. 打开文件，得到文件句柄并赋值给一个变量
2. 通过句柄对文件进行操作
	- 句柄：数据对象加载进内存之后即获得了地址，但是这个地址不是固定的，地址变动之后就需要有人来记录和管理变动，操作系统就用句柄来记载数据地址的变更（与指针不同）
3. 关闭文件

## 打开/关闭文件（句柄/描述符）
#### 打开文件
- python2与python3
	- python2中有两种打开文件的方法，一种是通过实例化`file`类，另一种是通过内置函数`open()`，建议使用open()
	- python3中只能使用内置函数`open()`
	
- 正确打开文件的姿势——使用上下文with管理打开的文件
	- `with open(filename, mode, encoding)`
		- encoding: 与操作系统相关。对于Linux系统，使用的是utf-8，而Windows则是GBK(中国地区)
			- 查看操作系统使用的编码`sys.getfilesystemencoding()` 
#### 关闭文件
- 既然打开一个文件包含两部分资源，那么正确的关闭必然要**保证两部分资源全部关闭**
- `f.close()`
	- 关闭的是操作系统级别的资源，对于应用程序级别的变量，会被python解释器垃圾回收机制清理
- `del fp`
	- 删除文件句柄/文件描述符只能关闭应用程序占用的资源。
	- **注意**： 使用`del fp`前应先使用`fp.close()` 


## 文件相关
#### 打开文件的方式 mode
- r	**只读模式**，文件必须存在，不存在则抛出异常，光标在文件开头
	- open()默认`r`模式
- w	**只写模式**，不可读，不存在则创建，**存在则清空源文件内容后覆写**，光标在文件开头
- x	**只写模式**，不可读，不存在则创建，**存在则报错，光标再文件开头**
- a	**追加模式**，可读，不存在则创建，存在则追加，光标再文件末尾
- r+   可读可写，光标在打开文件时在文件开头，可以一边写一边读。
- w+  无意义，因为打开时候，文件已经被清空，不过可以按照下面这样用，光标到指定位置，再读文件内容，如果这个时候再写入内容，会从文件末尾开始继续写入内容，而不只是当前位置。
- 在上述mode之后加上小写字幕`b`，就是二进制模式

#### 二进制模式与文本模式
- 保存在硬盘中的文件是字节类型的数据，因此如果使用二进制模式打开文件的时候不需要指定编码。



## 文件操作
#### 读取
- f.read(size=-1)
	- 读取文件所有内容，光标移动到文件末尾
	- size: size为正数的时候，返回size个的数据，为负数的时候调用readall()
		- 对于文本文件：size单位为字符
		- 对于二进制文件：size单位为字节
- f.readline(size=-1)
	- 返回字符串包含整个文件
	- 如果size>0，返回指定size个**字符**
- f.readlines(line_num = -1)
	- 返回一个存放每一行内容构成的列表
	- line_num: 大于零时，代表要读取的**行数**

 
#### 写入
- f.write()
	- Write the string s to the stream and return the number of characters written.
	- 返回值为写入的**字符**数
- f.writelines(lines)
	- Write a list of lines to the stream. Line separators are not added, so it is usual for each of the lines provided to have a line separator at the end

- f.flush()
	- 立刻将文件从内存刷入硬盘
	- 文件关闭之前自动调用

#### 获取文件信息


- f.readable()
- f.writeable()

- f.name
	- 属性
	- 返回文件名

- f.encoding
	- 属性
	- 返回打开文件的编码

- f.closed
	- 是个属性，不是方法


#### 修改
##### 方式一
- 将文件一次性读取到内存，修改完成之后再用`w`mode全部写入

##### 方式二
- 将文件读取到内存，修改完成后写入新文件，再用新文件覆盖旧文件

## 光标操作
- f.seek(offset[, whence=0])
	- 移动光标到指定位置
	- 返回值为代表当前位置的数字
	- parameters
		- offset： 偏移量
			- 对于文本对象，单位为字符character
			- 对于二进制对象，单位为字节bytes
		- whence
			- 0(SEEK_SET)：移动到stream开头
			- 1(SEEK_CUR)：移动到stream位置
			- 2(SEEK_END)：移动到stream末尾  

- f.tell()
	- 放回光标当前位置
	- 对于文本文件，返回的是字符单位
	- 对于二进制文件，返回的是字节单位

## 其他
1. 当不允许覆盖已存在的文件，而是在一个新的文件中写入数据，但是又不知道目标目录下是否有重名文件时候，应该采用 'x '模式来代替'w'模式来完成数据写入

```python
>>> with open('haha.txt', 'w') as h:
	h.write('haha\nziawang!')

	
13
>>> with open('haha.txt', 'x') as h:
	h.write('haha\nziawnag')

	
Traceback (most recent call last):
  File "<pyshell#7>", line 1, in <module>
    with open('haha.txt', 'x') as h:
FileExistsError: [Errno 17] File exists: 'haha.txt'
>>> 

							#这个时候就不用担心已有文件被覆写啦，x模式下，当已存在重名文件##时会报出FileExitsError

```

PS：如果文件是二进制的，就用xb代替x
**在python的旧版中是没有X模式的，要实现相同的功能，可以配合if...else**

```

>>> import os
>>> if not os.path.exists('haha.txt'):
	with open('haha.txt', 'w') as h:
		h.write('haha\nziawang')
else:
	print('File already exists! ')

	
File already exists! 
>>> 
```


2. open()返回的句柄是一个可迭代对象，内部实现了`__iter__()`方法