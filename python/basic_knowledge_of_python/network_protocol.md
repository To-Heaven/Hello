# 网络编程

[点击查看我整理的网络编程思维导图](https://github.com/ZiaWang/Hello/blob/master/picture/internet_program.png?raw=true)

## note
- __del__
	- 自定义在类中关闭对象占用资源的方法，比如文件和套接字打开之后的关闭



## 客户端/服务端架构(client/sever架构)
- 软件有操作硬件的需求，但是不能直接操纵硬件，需要通过操作系统来完成
- 硬件C/S架构
	- 打印机
- 软件C/S架构
	- qq，浏览器等
## 网络协议

> [网络协议介绍]()
> 单纯应用软件最初产生的数据没有任何意义，需要经过其他层的层层封装才能进行传输（上一层的内容作为下一层的数据部分）

- 以太网：基于mac地址实现通讯的协议，在局域网内以广播的方式通信
- ip协议：通过IP地址和子网掩码确定机器的子网范围和机器在子网内的位置
- arp协议：可以自动通过ip地址获取目标主机的mac地址
	- 有个一个ip地址就可以找到唯一一台电脑
- tcp/udp:基于端口的协议
- 自定义应用层协议
	- 自定义的协议报头必须是固定长度	！！
	- `struct`模块



## 基于tcp和udp协议的socket抽象层
- socket抽象层
	- socket是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口
		- 把复杂的TCP/IP协议族隐藏在Socket接口的后面
	- socket将除了应用层之外的协议封装起来，用户软件产生的数据只要遵守socket提供的协议标准就能进行对数据进行封装
	- 也可以将socket简单的看成IP+port
		- ip用来标识互联网中的一台主机的位置
		- port用来表示这台机器上的一个应用程序
		- ip和port的绑定就标识了互联网中独一无二的一个应用程序



## 套接字家族
- 套接字是什么鬼？
	- 源IP地址和目标IP地址以及源端口号和目标端口号的组合
	- 支持TCP/IP的网络通信的基本操作单元，是不同主机之间进行双向通信的端点

- 常用套接字有两种
	- 基于文件类型的套接字
		- AF_UNIX
	- 基于网络类型的套接字
		- AF_INET 
			- 对于网络编程来说，基本上只用AF_INET




- 套接字的类型
	- STREAM ---> tcp协议
	- SOCK_DGRAM ---> udp协议
	- SOCK_RAM





- 客户端和服务端都必须要提供ip地址和接口
	- 服务端需要绑定IP和port地址，因为服务端需要被访问
		- 服务端寻找客户端通过tcp的握手来完成互相连接
		- 通过connect()  accept()


### 服务器端套接字方法
- socket_obj.bind(tuple)
	- tuple 是 IP地址和 port 组成的元组，ip地址要用字符串
		- port
			- 0-1024系统占用
			- 自定义端口 1025-65535
			- 一台叫机器上一个端口只能绑定一个应用

- socket_obj.listen
	- listen()监听操作是操作系统完成的


- conn , clinet_address_port= socket_obj.accept 
	- 底层是三次握手，得到的是一个与客户端建立好的链接
	- 返回值是一个元组，第一个是得到的连接，第二个是客户端的ip和port组成的元组
		- 序列unpacking

- socket.close()
	- 程序报错无法运行到这步，操作系统就不能接收到关闭接口的请求
		- 操作系统也会检测无效的接口，不过有时间延迟

> 类似file.open函数
> 打开文件时存在应用软件级别和操作系统级别对文件的打开，关闭时要使用close，而不是del应用程序文件




## 简单套接字的CS架构

一个简单的服务端程序

```python
# sever.py 
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)      # 创建一个套接字对象
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)       # 在每次服务器重启时，解除上一次端口占用
server.bind(('127.0.0.1', 8080))        # 为服务端套接字绑定adress(IP, port)
server.listen(5)        # 服务端开始监听的方法
connect, adress = server.accept()       # unpacking，获取一个链接connect和一个客户端地址

data = connect.recv(1024)       # 获取客户端发送来的数据，是bytes类型
print(data)
connect.send(data+b'ziawang')       # 向客户端发送数据，也是bytes类型， 不能是字符串，会报错，不信你试试

connect.close()         # 关闭客户端与服务端之间建立的链接
server.close()          # 关闭服务端套接字

-------------------------------------------------------------------------------------
# 这样写更好
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    address = ('127.0.0.1', 8080)
    s.bind(address)
    s.listen(5)
    conn, client_addr = s.accept()
    with conn as c:
        while True:
            try:
                msg = c.recv(1024)
                if not msg:break
                print('recv from %s: %s'%client_addr)
                c.send(msg.upper())
            except Exception as e:
                print(e)
                break

```

一个简单的客户端程序

```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)      # 客户端也需要创建套接字对象
client.connect(('127.0.0.1', 8080))             # 链接目标服务器的adress(ip, port)

client.send('hello'.encode('utf-8'))            # 向目标服务器发送数据，必须是bytes类型，需要编码
data = client.recv(1024)                        # 从服务器接收数据
print(data)

client.close()                              # 关闭客户端套接字

-----------------------------------------------------------------
# 这样写更好
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM)  as c:
    server_addr = ('127.0.0.1', 8080)
    c.connect(server_addr)
    while True:
        msg = input('>>>').strip()
        if not msg:continue
        c.send(msg.encode('utf-8'))
        data = c.recv(1024)
        print(data.decode('utf-8'))
-----------------------------------------------------------------------------
# 如果你想写的更短小
import socket

server_addr = ('127.0.0.1', 8081)
with socket.create_connection(server_addr)  as c:
    while True:
        msg = input('>>>').strip()
        if not msg:continue
        c.send(msg.encode('utf-8'))
        data = c.recv(1024)
        print(data.decode('utf-8'))

```




## 带有通信循环的CS

服务端程序
```python
# sever.py 

import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 2)
server.bind(('127.0.0.1', 8080))
server.listen(5)
connect, adress = server.accept()

while True:         # 使服务端可以不断接收从客户端发送来的数据，并不断返回数据
    data = connect.recv(1024)
    print(data)
    connect.send(data+b'ziawang')

connect.close()         # 这两个close()函数不能放在while 循环中
server.close()

```

客户端程序

```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)      # 客户端也需要创建套接字对象
client.connect(('127.0.0.1', 8080))             # 链接目标服务器的adress(ip, port)

while True:
    msg = input('>>>')
    client.send(msg.encode('utf-8'))            # 向目标服务器发送数据，必须是bytes类型，需要编码
    data = client.recv(1024)                        # 从服务器接收数据
    print(data)

client.close()                              # 关闭客户端套接字

```

交互的结果


```python
# 客户端
>>>a
b'aziawang'
>>>b
b'bziawang'
>>>c
b'cziawang'
>>>
Process finished with exit code 1
```


-   当把客户端的程序关闭后，服务端程序仍然继续运行，此时connect链接会终端，服务端函数无法正常运行就会报错



```python
# 服务端
b'a'
b'b'
b'c'
Traceback (most recent call last):
  File "D:/files/python_practice/0821-0827/0821/sever.py", line 38, in <module>
    data = connect.recv(1024)
ConnectionResetError: [WinError 10054] 远程主机强迫关闭了一个现有的连接。

```


- 客户端发送空内容
	- 上面的测试中，如果在client的input交互中直接按enter输入了空内容，那么client就会卡在
	- 输入空格，服务器未接收到任何内容所以就不会向客户端发送任何内容，而此时client就会卡在client.recv()函数上

```python
# 服务端内容不变
# 客户端

import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)     
client.connect(('127.0.0.1', 8080))           

while True:
    msg = input('>>>')
    client.send(msg.encode('utf-8'))            
    print('卡在recv方法位置')		# 运行client.recv()之前
    data = client.recv(1024)                    
    print('无法运行到这一句')		# 在client.recv()之后，无法运行到
    print(data)

client.close()                           

```
	
客户端

```python
>>>
卡在recv方法位置


```

服务端

```python
（未接收到任何内容）
```

- 解决上面的方法很简单，在input之后对msg进行if判断即可

```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)     
client.connect(('127.0.0.1', 8080))         

while True:
    msg = input('>>>')
    if not msg: continue					#  添加判断
    client.send(msg.encode('utf-8'))         
    data = client.recv(1024)                     
    print(data)

client.close()                           

```


- 还有一个问题没有解决，就是在客户端执行  client.close() 或者直接关闭客户端程序的时候，服务端会报错崩溃
	- 这种错误会导致服务段函数无法执行后面   connect.close() 和server.close()，如果这里不使用setsockpt()方法，那么下次启动服务端，这个端口将无法被新启动的服务端使用

```python
# 服务端
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 2)
server.bind(('127.0.0.1', 8080))
server.listen(5)
connect, adress = server.accept()

while True:
    data = connect.recv(1024)
    print(data)
    connect.send(data+b'ziawang')

connect.close()         # 这两个close()函数不能放在while 循环中
server.close()


```

```python
# 客户端

import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)   
client.connect(('127.0.0.1', 8080))          

while True:
    msg = input('>>>')
    if not msg: continue
    if msg == 'quit':break					# 当输入quit的时候，跳出循环
    client.send(msg.encode('utf-8'))          
    data = client.recv(1024)                      
    print(data)

client.close()                         
```

- 运行结果

客户端

```python

>>>a
b'aziawang'
>>>quit

Process finished with exit code 0
```

服务端

```python
b'a'
Traceback (most recent call last):
  File "D:/files/python_practice/0821-0827/0821/sever.py", line 38, in <module>
    data = connect.recv(1024)
ConnectionAbortedError: [WinError 10053] 你的主机中的软件中止了一个已建立的连接。
b''

Process finished with exit code 1
```


- 遇到这种情况，应对服务端程序使用try语句

```python
# 修改后的服务端， 客户端不变
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 2)
server.bind(('127.0.0.1', 8080))
server.listen(5)
connect, adress = server.accept()

while True:
    try:
        data = connect.recv(1024)
        print(data)
        connect.send(data+b'ziawang')
    except Exception:
        break

connect.close()         # 这两个close()函数不能放在while 循环中
server.close()

```

- 运行结果

```python
# 客户端
>>>quit

Process finished with exit code 0
```

```python
# 服务端
b''

Process finished with exit code 0

```


## 加上链接循环的CS

> 即，使用多个客户端去连接服务端

- 当用多个客户端去连接服务端的时候，如果上面服务端代码保持不变，那么服务端就只会与最先accept接收到的客户端进行交互，不管后面再运行多少个客户端，服务端都不会搭理。
	- 这样会导致没连接上服务端的这些客户端虽然能向客户端发送信息，但是客户端并不接收他们的信息，导致卡在了client.recv()函数前
	- 这些不能连接上服务端的客户端程序，在服务端关闭之后会报错，错误内容为'远程主机关闭了连接'
- 上代码

```python
# 服务端
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 2)
server.bind(('127.0.0.1', 8080))
server.listen(5)
connect, adress = server.accept()

while True:
    try:
        data = connect.recv(1024)
        print(data)
        connect.send(data+b'ziawang')
    except Exception:
        break

connect.close()      
server.close()

```

```python
# 连接上的客户端


import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)   
client.connect(('127.0.0.1', 8080))           

while True:
    msg = input('>>>')
    if not msg: continue
    if msg == 'quit':break
    client.send(msg.encode('utf-8'))            
    data = client.recv(1024)                     
    print(data)

client.close()                             

```


```python
# 未连接上的客户端

import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)     
client.connect(('127.0.0.1', 8080))           

while True:
    msg = input('>>>')
    if not msg: continue
    if msg == 'quit':break
    client.send(msg.encode('utf-8'))          
    print('我卡在了这儿')
    data = client.recv(1024)                    
    print('这里的输出不了')
    print(data)

client.close()                             

```

- 运行结果

```python
# 服务端
b'a'
b'b'
b''

Process finished with exit code 0
```



```python
# 能连接上的客户端
>>>a
b'aziawang'
>>>b
b'bziawang'
>>>quit

Process finished with exit code 0
```


```python
# 不能连接上服务端的客户端

>>>a
我卡在了这儿
Traceback (most recent call last):
  File "D:/files/python_practice/0821-0827/0821/client1.py", line 37, in <module>
    data = client.recv(1024)                        # 从服务器接收数据
ConnectionResetError: [WinError 10054] 远程主机强迫关闭了一个现有的连接。

Process finished with exit code 1
```

- 要实现多个客户端连接一个服务端，可以在服务端加上一个循环，客户端会按先后顺序连接服务端，当一个客户端断开与服务端的链接之后，下一个客户端就会连接上服务端
	- 服务端的listen()方法用来限制等待连接服务端的客户端的个数
	- 上代码

```python
# 改编后的服务端程序


import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 2)
server.bind(('127.0.0.1', 8080))
server.listen(5)

while True:
    connect, adress = server.accept()		# accept 用来接收多个客户端
    while True:
        try:
            data = connect.recv(1024)
            print(data)
            connect.send(data+b'ziawang')
        except Exception:
            break
    connect.close()        
server.close()

```


```python
# 客户端a, b, c程序代码相同
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)    
client.connect(('127.0.0.1', 8080))            

while True:
    msg = input('>>>')
    if not msg: continue
    if msg == 'quit':break
    client.send(msg.encode('utf-8'))           
    data = client.recv(1024)                        
    print(data)

client.close()                        
```

- 运行过程

```python
# 服务端

b'a'
b''
b'b'
b''
b'c'
b''

```

```python
# 客户端a
>>>a
b'aziawang'
>>>quit

Process finished with exit code 0
```


```python
# 客户端b
>>>b
b'bziawang'
>>>quit

Process finished with exit code 0
```

```python
# 客户端c
>>>c
b'cziawang'
>>>quit

Process finished with exit code 0
```

- 注意，在linux系统中，服务端是可以接收空内容的，因此要在服务端server.recv()之后添加上一个判断
	- windows中服务器无法接受空内容

```python

import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 2)
server.bind(('127.0.0.1', 8080))
server.listen(5)

while True:
    connect, adress = server.accept()
    while True:
        try:
            data = connect.recv(1024)
            print(data)
            if not data: break					# 使用于Linux系统！！！！！！
            connect.send(data+b'ziawang')
        except Exception:
            break
    connect.close()         
server.close()
```



## 基于tcp协议模拟ssh远程执行命令
- 先做一个简单的远程cs

```python
# 服务端

from socket import *
import subprocess


server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
server.bind(('127.0.0.1', 8080))
server.listen(5)

while True:
    connect, addr = server.accept()
    while True:
        try:
            cmd = connect.recv(1024)
            if not cmd:continue  # linux
            res = subprocess.Popen(cmd.decode('utf-8'), shell=True,
                                   stdout=subprocess.PIPE,
                                   stderr=subprocess.PIPE)
            stdout = res.stdout.read()
            stderr = res.stderr.read()

            connect.send(stdout + stderr)
        except Exception:
            break
    connect.close()

server.close()

```


```python
# 客户端


from socket import *

client = socket(AF_INET, SOCK_STREAM)
client.connect(('127.0.0.1', 8080))

while True:
    cmd = input('>>').strip()
    if not cmd:continue
    client.send(cmd.encode('utf-8'))
    res = client.recv(1024)
    print(res.decode('gbk'))
client.close()

```

- 看客户端运行结果
```python

>>dir                                                                                                                                    # 执行dir命令
 驱动器 D 中的卷没有标签。
 卷的序列号是 5610-A12F

 D:\files\python_practice\0821-0827\0822 的目录

2017/08/22  15:15    <DIR>          .
2017/08/22  15:15    <DIR>          ..
2017/08/22  15:09               396 client.py
2017/08/22  08:12             1,157 practiec.py
2017/08/22  15:15               870 server.py
               3 个文件          2,423 字节
               2 个目录 89,094,680,576 可用字节

>>ifconfig                                                                                                                             # 执行错误命令                       
'ifconfig' 不是内部或外部命令，也不是可运行的程序
或批处理文件。

>>ipconfig                                                                                                                             # 执行ipconfig，结果并没有全部输出

Windows IP 配置


无线局域网适配器 WLAN:

   媒体状态  . . . . . . . . . . . . : 媒体已断开连接
   连接特定的 DNS 后缀 . . . . . . . : DHCP HOST

无线局域网适配器 本地连接* 2:

   媒体状态  . . . . . . . . . . . . : 媒体已断开连接
   连接特定的 DNS 后缀 . . . . . . . : 

无线局域网适配器 本地连接* 4:

   媒体状态  . . . . . . . . . . . . : 媒体已断开连接
   连接特定的 DNS 后缀 . . . . . . . : 

以太网适配器 以太网:

   连接特定的 DNS 后缀 . . . . . . . : 
   本地链接 IPv6 地址. . . . . . . . : fe80::55a0:da45:7e4b:12bf%21
   IPv4 地址 . . . . . . . . . . . . : 192.168.20.27
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . : 192.168.20.254

以太网适配器 VMware Network Adapter VMnet1:

   连接特定的 DNS 后缀 . . . . . . . : 
   本地链接 IPv6 地址. . . . . . . . : fe80::5c4b:e23a:aa0a:9efc%11
   IPv4 地址 . . . . . . . . . . . . : 192.168.126.1
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . : 

>>aaaa                                                                                               # 执行一个错误命令， 发现并没有报出相应错误，而是接上面命令内容

以太网适配器 VMware Network Adapter VMnet8:

   连接特定的 DNS 后缀 . . . . . . . : 
   本地链接 IPv6 地址. . . . . . . . : fe80::e11f:99c7:8c24:b423%9
   IPv4 地址 . . . . . . . . . . . . : 192.168.20.1
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . : 

以太网适配器 以太网 2:

   媒体状态  . . . . . . . . . . . . : 媒体已断开连接
   连接特定的 DNS 后缀 . . . . . . . : 

隧道适配器 isatap.{CD5EB8DB-B981-4F35-97B1-F374136BAD4E}:

   连接特定的 DNS 后缀 . . . . . . . : 
   本地链接 IPv6 地址. . . . . . . . : fe80::5efe:192.168.20.27%6
   默认网关. . . . . . . . . . . . . : 

隧道适配器 Reusable ISATAP Interface {9B62E0A8-086B-4D26-8057-C20E13CBC4F7}:

   连接特定的 DNS 后缀 . . . . . . . : 
   本地链接 IPv6 地址. . . . . . . . : fe80::5efe:192.168.126.1%16
   默认网关. . . . . . . . . . . . . : 

隧道适配器 isatap.{24D50D85-601B-4C2C-91AC-D68FA6AF6B60}:

   连接特定的 DNS 后缀 . . . . . . . : 
   本地链接 IPv6 地址. . . . . . . . : fe80::5e
>>aaa                                                                                                                                   # 再运行一个错误命令，ipconfig的结果全部输出之后，才打出了错误信息
fe:192.168.20.1%8
   默认网关. . . . . . . . . . . . . : 

以太网适配器 蓝牙网络连接:

   媒体状态  . . . . . . . . . . . . : 媒体已断开连接
   连接特定的 DNS 后缀 . . . . . . . : 

隧道适配器 本地连接* 5:

   媒体状态  . . . . . . . . . . . . : 媒体已断开连接
   连接特定的 DNS 后缀 . . . . . . . : 
'aaaa' 不是内部或外部命令，也不是可运行的程序
或批处理文件。

>>
Process finished with exit code 1
```

> 出现这种情况的原因是在服务器端缓存区产生了粘包，具体看下面


## 粘包现象
> 应用软件想要经过网卡来发送数据，必须得经过操作系统，因为应用软件不能直接调用计算机硬件        
> 应用软件将要发送的数据交给操作系统，操作系统发起调用网卡的系统调用，并按照对应的协议将缓存池中的数据经网卡传输给其他计算机 

- socket收发信息的原理
	- 应用程序想要把请求发送到服务器，就得经过本地主机的网卡，因此就需要进过操作系统来控制网 卡（应用程序不能直接控制硬件，需要通过操作系统）
	- 操作系统接收到该请求之后，会将该请求先存放在本地内存的一个缓存区，经过tcp协议Nagle算法的处理，将缓存区中的请求封装成一个包（可能会和其他满足Nagle算法的小包封装在一起），再经过网卡将数据包经由internet传送到服务器主机
	- 服务器主机接收到该数据包之后，将该数据包存放在主机内存缓存中，根据recv指定长度进行接收缓存池中的数据
		- 上述过程中牵扯到了cpu内核态和用户态之间的相互切换，当应用程序发出调用网卡的请求时，cpu的运行状态就从用户态转换成内核态

- Nagle算法
	- 目的：网络传输中如果交互式的传递大量的小包，就会导致大量重复的IO操作，并产生了较长的IO延迟。Nagle算法就是将传输中的时间间隔非常短，并且一个个小包的数据量较小的数据合并成一个打的数据块，再进行封包
		- 小包之间时间间隔短
		- 小包的数据量比较小
	
- 先看一下TCP协议（socket中就是AF_INET）
	- TCP面向连接的，面向流的，提供可靠性服务的数据传输协议
	- 发送端为了将多个包更有效的传输给接收端，采用了Nagle算法
	- 因此**只有tcp才会出现粘包现象，udp永远不会粘包**



- **粘包**
	- 主要是因为接收方不知道消息之间的界限，导致不知道一次性提取多少字节的数据导致的
	- 在一个较短的时间内，发送方引起的粘包主要是因为TCP协议本身造成的，TCP为了提高传输效率，发送方往往需要收集到足够多的数据之后才能发送一个TCP段。
		- 如果很短时间间隔内连续发送的几个数据包都很小，TCP会使用Nagle算法将这些数据合成一个TCP段后再一次性发送出去，导致接收放收到的是一个粘包数据

模拟一个简单的粘包数据的发送

```python
#  服务端
import  socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    addr = ('127.0.0.1', 8080)
    s.bind(addr)
    s.listen(5)
    conn, client_addr = s.accept()
    with conn as c:
        msg = c.recv(1024)
        print(msg)
```


```python
# 客户端


import socket

server_addr = ('127.0.0.1', 8080)
with socket.create_connection(server_addr)  as c:
    c.send(b'hello')
    c.send(b'ziawang')
```

结果

```python
# 服务端结果
b'helloziawang'
```

- 发现客户端分两次发送的内容被打包成一个发送给服务端了
- 这样有时会导致程序崩溃，比如一个utf-8的汉字是3个bytes字节，服务端recv的长度也是可以定义的，如果这两个字节被分在两个包里面被在服务端recv时分散到两个包中，那么将会报错

```python
# 服务器
from socket import  *

server = socket(AF_INET, SOCK_STREAM)
server.bind(('127.0.0.1', 8080))
server.listen(5)

connect, addr = server.accept()
res1 = connect.recv(1)
print(res1)
res2 = connect.recv(1)
print(res2)

print(res1.decode('utf-8'))

connect.close()
server.close()
```

```python
#客户端
from socket import *

client = socket(AF_INET, SOCK_STREAM)
client.connect(('127.0.0.1', 8080))
print('你'.encode('utf-8'))
client.send('你'.encode('utf-8'))

client.close()
```

服务器结果

```python
b'\xe4'
b'\xbd'
b'\xa0'
Traceback (most recent call last):
  File "D:/files/python_practice/0821-0827/0822/server.py", line 49, in <module>
    print(res1.decode('utf-8'))
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xe4 in position 0: unexpected end of data

Process finished with exit code 1
```


客户端结果

```python
b'\xe4\xbd\xa0'
```


- 现在再回头看，之前模拟ssh远程命令的程序之所以出现那种情况，就是因为服务端发送的数据经过internet发送到目标客户端主机后，存放在缓存池中数据会根据客户端的recv()来进行对应的数据接收，当缓存中的数据没有被本次recv()完的时候，下一次的recv()就会继续接收该缓存中的内容

- 解决这种粘包现象，可以使用控制时间的方法

```python
# 服务端

from socket import  *

server = socket(AF_INET, SOCK_STREAM)
server.bind(('127.0.0.1', 8080))
server.listen(5)

connect, addr = server.accept()
res1 = connect.recv(5)
print(res1)
res2 = connect.recv(10)
print(res2)


```

```python
# 客户端
import time
from socket import *

client = socket(AF_INET, SOCK_STREAM)
client.connect(('127.0.0.1', 8080))
client.send('hello'.encode('utf-8'))
time.sleep(5)
client.send('ziawang'.encode('utf-8'))
```

```python
# 服务端运行结果
b'hello'
b'ziawang'


Process finished with exit code 0
```

## 自定义协议解决粘包

> 为字节流加上固定长度报头head，head中包含要send的字节流的的长度，然后一次send到接收端，接收端在接收的时候，会先从缓存中recv()固定报头长度的head，然后再根据head中存储的字节流长度来取真实数据

- struct 模块
	- 把一个类型的数据转换成固定长度的bytes
	- struct.pack(format, data)
		- 返回值是一个被转换后的bytes类型数据
		- format: 要被转换的数据的数据类型对应的格式字符串
			- '  i  '  	int		返回的长度  4bytes
			- '   f  '       float	返回的长度  4bytes
			- bool        ?		返回的长度  1bytes
		- pack方法转换的' i '也是有范围的
			- 2147483648 < = number <= 2147483648
			- 对应的i能够描述的文件的大小是大概  2G
	- struct.unpack(format, pack_obj)
		- format 是对应struct方法的格式
		- pack_obj是struct方法的返回值，这个方法可以将bytes转换成对应的数据
	 	- 返回值是一个元组，元组第一个元素存放的就是最初的该数据

```python
import struct


res = struct.pack('i', 123456789)
print(res)

res_unpack = struct.unpack('i', res)
print(res_unpack)

-------------------------------------------------------------------------------
b'\x15\xcd[\x07'
(123456789,)
```


- 由于   i  有最大长度限制，因此当要处理的文件远大于这种情况的时候，就可以再内存中分块的读取这个大文件，继续上面ssh远程命令程序，假设现在服务器端返回了一个8G的大数据
- 如果tcp中自定义的head报头只包含报头的长度信息，那就太简单了。还可以在head中还要包含有文件名，md5， 文件大小， 文件类型等信息
	- 将这些信息封装进报头，存储信息的容器最好应该是一个字典
	- 在传输中，要将字典序列化，再转换成bytes类型之后，再传输。这样客户端就可以将收到的json对象中的dict提取出来了

```python
# 服务端

```



```python
# 客户端


```





```python


```




- 利用tcp上传下载文件

https://www.processon.com/mindmap/599d051ce4b0d65f65d50bac


```python


```


## udp协议
- 特点
	- 无链接的，客户端和服务端那一个先启动都没有关系
	- socket中，使用SOCK_DGRAM来创建udp套接字
	- 每一个基于udp发送的数据包都自动封装上一个报头head
	- 每一个udp包的稳定传输大小是512字节，超过这个长度的数据部分发送可能会丢失
- 与tcp的区别
	1. tcp是安全的传输，而udp是不安全的传输
	2. tcp在客户端和服务端之间建立了双向链接，而udp不会再两者之间建立连接
		- 因此udp可以实现一个服务器与多个客户端进行交互的场景
		- tcp是基于链接的，必须先启动服务器，然后再启动客户端去连接客户端
	3. tcp协议会出现粘包现象，而udp协议就不会出现粘包现象
		- tcp协议发送的包是没有报头的，需要自定义创建一个报头
		- udp协议自动给每一个数据包封装了一个报头 
			- 因此即使发送端发送了一个空内容，也会被封装上报头，不会在接收端出现姐接收空内容而卡住的情况

	4. udp套接字不需要实现accept()方法，但是要创建一个无限循环来不断接收发送端的信息
- 关于安全性
	- tcp之所以安全，不是因为tcp建立了客户端与服务端之间建立了链接
		- 注意：tcp传输数据的时候，发送端先把数据发送到自己的缓存中。tcp协议会将缓存中的数据发往接收端，发送成功时，接收端会返回一个ACK=1，发送端收到这个确认信息之后，就会清理缓存中的数据。如果接收端没有收到数据，就会返回一个ACK=0，发送端接收到该信息之后不会清理缓存中数据，而是继续重新发送数据，因此tcp时可靠的
	- udp是不建立链接，因此每次发送端发送完数据包之后，就会自动清空缓存中的数据，而不会保留。 



```python
# 服务端
import socket

udp_server = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
udp_server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
udp_server.bind(('127.0.0.1', 8080))

while True:
    data, client_addr = udp_server.recvfrom(1024)
    print(data, 'from' ,client_addr)
    udp_server.sendto(data.upper(), client_addr)

udp_server.close()
```

```python
# 客户端


import socket


udp_client = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)


while True:
    msg = input('>>>').strip()
    udp_client.sendto(msg.encode('utf-8'), ('127.0.0.1', 8080))
    data, server_addr = udp_client.recvfrom(1024)
    print(data.decode('utf-8'))

udp_client.close()
```

```python
# 客户端


import socket


udp_client = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)


while True:
    msg = input('>>>').strip()
    udp_client.sendto(msg.encode('utf-8'), ('127.0.0.1', 8080))
    data, server_addr = udp_client.recvfrom(1024)
    print(data.decode('utf-8'))

udp_client.close()
```



```python
# 客户端1

>>>client one
CLIENT ONE
>>>

>>>
Process finished with exit code 1
```

```python
# 客户端2
>>>client two
CLIENT TWO
>>>

>>>
Process finished with exit code 1
```

```python
# 服务端

b'client one' from ('127.0.0.1', 64472)
b'client two' from ('127.0.0.1', 64473)
b'' from ('127.0.0.1', 64472)
b'' from ('127.0.0.1', 64473)

Process finished with exit code 1
```

- 对udp进行粘包测试
	- 接收端未接收到的信息

```python
# 服务端

server = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server.bind(('127.0.0.1', 8080))

data1, addr = server.recvfrom(1024)
print(data1.decode('utf-8'))

data2, addr2 = server.recvfrom(1024)
print(data2.decode('utf-8'))

data3, addr3 = server.recvfrom(1024)
print(data3.decode('utf-8'))

```


```python
# 客户端
client = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server_addr = '127.0.0.1', 8080

client.sendto(b'hello', server_addr)
client.sendto(b'world', server_addr)
client.sendto(b'happy', server_addr)


```

```python
# 服务端结果
hello
world
happy

Process finished with exit code 0
```



- 如果客户端发送的数据长度大于了服务端接收长度的限制 recvfrom(num)
	- 在windows系统上，会报错
	- 在Linux或OSx系统上不会报错，但是只会接收一部分

```python
# 服务端代码
server = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server.bind(('127.0.0.1', 8080))

data1, addr = server.recvfrom(1)
print(data1.decode('utf-8'))

data2, addr2 = server.recvfrom(1024)
print(data2.decode('utf-8'))

data3, addr3 = server.recvfrom(1024)
print(data3.decode('utf-8'))

```


```python
# 结果
Traceback (most recent call last):
  File "D:/files/python_practice/0821-0827/0823/onclass_server.py", line 26, in <module>
    data1, addr = server.recvfrom(1)
OSError: [WinError 10040] 一个在数据报套接字上发送的消息大于内部消息缓冲区或其他一些网络限制，或该用户用于接收数据报的缓冲区比数据报小。

Process finished with exit code 1
```


## udp实现聊天室




## s.setblocking(False)实现并发

```python
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    address = ('127.0.0.1', 8080)
    s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    s.bind(address)
    s.listen(5)
    s.setblocking(False)
    conn_l = []
    while True:
        try:
            conn, client_addr = s.accept()
            print('%s: %s 链接成功'%client_addr)
            conn_l.append(conn)
        except BlockingIOError:
            for c in conn_l:
                try:
                    msg = c.recv(1024)
                    if not msg:continue
                    c.send(msg.upper())
                except ConnectionResetError:
                    pass
                except Exception as e:
                    continue
        except Exception as e:
            print(e)
            break

```























