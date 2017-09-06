# 字符编码

[点击查看我整理的字符编码思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/character_encoding.png?raw=true)

## Note
- **用什么编码存，就用什么编码取**
	- 内存中固定使用Unicode编码，我们可以控制的就是存放在硬盘中或基于网络传输选择的编码（一般为utf-8）
- **数据最先产生于内存，要想传输需要转换成bytes格式**（即在内存中将数据由Unicode编码的二进制encode成指定编码（utf-8）的bytes格式）


```
Unicode------->encode(指定编码如utf-8)-------->bytes（bytes才能用于传输）
拿到bytes，就可以网文件内存放或者基于网络传输
bytes---------->decode(终端)---------->Unicode
```

- python3中字符串被识别成Unicode编码格式
	- python3中的字符串encode得到bytes

- python2中的字符串相当于bytes，python2中加u就是Unicode
	 

## 字符编码是什么鬼？
> 计算机要想工作就必须通电，而电传递的信号就是一个个高低电频（高电频即二进制中的1， 低电频为二进制中的0），计算机只认识数字
> 编程的本质要实现的是用一堆字符驱动计算机工作
> 二进制最终都是以电磁的方式存储到存储介质中

- 计算机如何识别指令?
	- 发送一堆字符给计算机的过程是这样的：
		- `字符------>（翻译过程）-------->  数字（二进制）`
		- 翻译过程就是字符应对一个特定转换成数字标准（字符编码），转换成计算机能看懂的二进制的过程
- 字符编码
	- 一个字符就对应着一个Bytes（一串二进制数，1个bytes等于8个bit位），位数越多，能表示的变化就越多，能容纳的字符就越多


## Unicode编码标准
- 统一使用2Bytes代表一个字符，可以表示2**16-1种变化，代表六万多个字符
- 缺点：占用空间大，每个字符都是2Bytes
- 优点： 数字转换速度快

## utf-8
- 不同字符用不同长度表示，对英文用1个Bytes表示，对中文用3个Bytes表示
- 缺点： 字符转换成数字的转换速度慢，每次都需要算出字符需要多长的Bytes才能够精确表示
- 有点： 节省空间，精准



## encode和 decode是么子鬼？
- 将Unicode编码的数据转换成其他编码的内容的过程就是encode
- 将其他字符编码的数据转换Unicode编码的内容的过程就是decode


## unicode与utf-8的使用场景
- unicode
	- 内存中使用
	- 以空间换时间——程序都需要加载到内存中才能运行（内存应该是尽可能保证快）

- utf-8
	- 硬盘或网络传输中使用
	- 网络I/O延迟和磁盘I/O延迟要远大于utf-8的转换延迟（网络I/O中应尽可能节省带宽，保证数据传输的稳定性）


> 所有程序最终都要加载进内存，程序本身代码保存在硬盘   
> 不同的国家采用不同的编码格式，但是到了内存中为了兼容多个国家，所以要统一固定使用unicode，这是为什么计算机可以运行任何国家程序的原因。这就是空间换时间的一种做法     
> 而放到硬盘上或者将数据用于网络传输，就需要把Unicode码转换成utf-8的 编码，这是因为数据传输追求的稳定高效，数据量越小数据传输就越靠谱。
 
## 文本编辑器中的编码的转换过程
![](http://https://github.com/ZiaWang/img/blob/master/%E6%96%87%E6%9C%AC%E7%BC%96%E8%BE%91%E5%99%A8%E7%9A%84%E7%BC%96%E7%A0%81%E8%BD%AC%E6%8D%A2.png?raw=true)
- 存文件
	- 文件数据由内存刷到硬盘的操作
- 读文件
	- 文件数据从硬盘读到内存的操作

## 乱码？？？？
- **在.py文件被python解释器执行之前，存放了代码的文件也仅仅是普通文件而已**
	- python3默认utf-8，python2默认ascii
- 存文件时候乱码（数据的损坏）
	- 存文件时，文件中有多个国家的文字，而只用了其中一个国家的编码（你如GBK）去存文件。
	- 本质上其他国家的文字在GBK编码中没有找到对应关系而存储失败。用编辑器存文件时候，编辑器会帮我们做转换，使其他语言也用GBK编码格式存储，导致了在存文件的阶段就产生了乱码。
- 读文件时候乱码，而存的时候不乱
	- 主要是因为存文件时用的是utf-8编码，不会乱码，而读文件的时候选择了错误的解码方式，这种**读文件阶段产生的乱码是可以解决的，而存文件时候的乱码会损坏文件**
- 无论使用何种编辑器，要防止文件出现乱码，核心的法则就是文件以什么编码保存，就以什么编码方式打开

## python解释器执行.py文件的过程
1. 启动python解释器
2. 在文件被执行前，python解释器的功能就是一个文本编辑器，负责打开.py文件，即从硬盘中读取.py文件的内容到内存中
	- python读取.py文件第一行内容 `#coding:utf-8`，**决定了该以什么编码格式来解码decode并读入内存**，这一行设定了python解释器这个软件的编码使用的编码格式是utf-8
		- 可以使用sys.getfilesystemencoding()查看
	- `#-*-coding:utf-8-*-`   如果没在文件第一行声明指定编码，就使用操作系统默认的编码


```python
>>> import sys
>>> sys.getfilesystemencoding()
'utf-8'
>>> 
```


3. 读取加载到内存的代码（默认为Unicode码的二进制），然后执行。执行过程中按照python语法，遇到变量会开辟内存空间
	- **注意**：内存中编码使用的是Unicode，不代表内存中全都是Unicode编码的二进制数据
	- 程序执行前，内存中确实都是Unicode编码的二进制，但是在程序执行的过程中，会申请内存（与程序代码所存在的内存是两个空间），可以存放指定编码格式的数据，比如name = 'ziawang'，被python解释器识别为字符串，然后申请内存空间存放'ziawang'，然后让name指向该内存的地址，此时保存的'ziawang'是Unicode编码的，如果代码换成这样  name = 'ziawang'.encode('utf-8')，内存中存放的就是utf-8编码的字符串'ziawang'了。
- `.py`文件被python解释器编码解码，也就是第二步之后，第三部文件中的语法错误才会被报出。
- python3中字符串都是Unicode格式的二进制存放在内存中，而在python2中，字符串都是已经encode后的结果，即Bytes
## 网络传输中的编码转换
- 浏览网页的时候，服务器会把动态生成的Unicode内容转换成utf-8再传输给浏览器。
	- 如果服务端encode的编码格式是utf-8，客户端内存中收到的也是utf-8编码的二进制。
![](http://https://github.com/ZiaWang/img/blob/master/%E7%BD%91%E7%BB%9C%E4%BC%A0%E8%BE%93%E4%B8%AD%E7%BC%96%E7%A0%81%E8%BD%AC%E6%8D%A2.png?raw=true)
- 字符串要想传输必须转换成Bytes类型，Bytes是由Unicode 类型字符串按照指定的编码encode过来的。在python3中，字符串只有一个encode方法，因为python3中的字符串默认为Unicode编码格式，而在python2中，字符串已经默认由Unicode编码格式的字符串encode成的Bytes类型数据，即已经进过encode处理过

## python3与python2 中的的字符串
###python2中有两种字符串类型str和Unicode
- 当python解释器执行到产生字符串的代码时，会申请新的内存地址，然后**将变量值encode成文件开头指定的编码格式**，这已经是encode的结果了，所以只能decode。
	- **所以在python2中，str就是bytes类型的数据。**所以在python2中，unicode字符编码的结果就是str/bytes

```python
Python 2.7.13 (v2.7.13:a06454b1afa1, Dec 17 2016, 20:53:40) [MSC v.1500 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> s = 'aaa'
>>> type(s)
<type 'str'>
>>> type(s) is bytes
True
>>> s.decode('ascii')
u'aaa'
>>> s.decode('utf-8')
u'aaa'
>>> s.decode('gbk')
u'aaa'
>>>

```





