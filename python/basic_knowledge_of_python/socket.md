# socket模块

[点击访问更高级的socket接口socketserver]()
[点击访问网络通讯原理]()

> Unix、BSD、Linux 之间的关系<br>
> BSD(伯克利软件套件)是Unix操作系统的一个分支，而Linux则是一个类Unix的系统，并不是Unix系统的一个分支

## The Adress of  Socket Families


## Module_level  Contents 
- the adress(protocol) families ----the first argument for socket()
	- socket.AF_INET     --->  IPv4
	- socket.AF_UNIX    --->  linux & unix

- socket types 
	- socket.SOCK_STREAM    --->  TCP
	- socket.SOCK_DGRAM    --->  UDP

- used for judgement
	- socket.has_ipv6
		- True if IPv6supported on this platform

## Module_level  Functions For Create Sockets
- socket.socket(family=AF_INET, type=SOCK_STREAM, proto=0, fileno=None)
	- return a new socket using the given address family, socket type and protocol number
	- The protocol number is usually zero and may be omitted（基本不用）
	- The newly created socket is non-inheritable.


- socket.create_connection(address[, timeout[, source_address]])
	- return a socket object 
	- this is a high-level function than socket.connect()
	- PARAMETERS
		- address: a 2-tuple of host and port   (ip, port), the host(ip) should be a numeric hostname，这个是要链接的服务端的adress
		- timeout: set the timeout on the socket instance before attempting to connect.
			- __use  getdefaulttimeout() while this arhument is not supplied__
		- source_adress: a 2-tuple (host, port) for the socket to bind to as its source address before connecting，即客户端的adress

## Other Module_level Functions
- socket.gethostbyname(hostname)
	-  return a **string** which is translated by a host name to IPv4 address format
	-   the host name is an IPv4 address itself it is returned unchanged
	-   **this function do not support for IPv6**




- socket.gethostbyname_ex(hostname)
	- return a **tuple ** of (hostname, alialist, ipaddrlist)
	- host name is an IPv4 address itself it is returned unchanged
	- alialist is a (possibly empty) list of alternative host names for the same address
	- ipaddrlist is a list of IPv4 addresses for the same interface on the same host (often but not always a single address)
	- **this function do not support for IPv6**

- socket.gethostbyaddr(ip_adress)
	- Return a triple (hostname, aliaslist, ipaddrlist)

- socket.gethostname()
	- Return a string containing the hostname of the machine where the Python interpreter is currently executing.
- socket.sethostname(name)
	- Set the machine’s hostname to name.
	- raise an OSError if you don’t have enough rights.
	- **just useful for Unixs**


```python
import socket


my_hostname= 'www.ziawang.com'
my_host = socket.gethostbyname(my_hostname)
my_host_tuple = socket.gethostbyname_ex(my_hostname)
print(my_host)
print(my_host_tuple)

hostname = socket.gethostname()
print(hostname)
------------------------------------------------------------------------------------
104.27.177.209
('www.ziawang.com', [], ['104.27.177.209', '104.27.176.209'])
王子豪

Process finished with exit code 0
```


- socket.getservbyport(port[protocolname])
	- Translate an Internet port number and protocol name to a service name for that service
	- The optional protocol name, if given, should be 'tcp' or 'udp', otherwise any protocol will match.

```python


```


## socket Object Methods for Server
- socket.accept()
	- Accept a connection.
	- The return value is a pair (conn, address)
		- conn is a new socket object of client, useable to send and recv data on the connection
		- adress is the adress bound to the socket of new client

- socket.bind(adress)
	- Bind the socket to address
	- The socket must not already be bound
	- **the format of adress depens on the adress family**
		- AF_INET    (ip, port )

- socket.close()
	- Mark the socket closed and all future operations on the socket object will fail
	- The remote end will receive no more data (after queued data is flushed).
	- **Sockets are automatically closed when they are garbage-collected**
	-  use the 'with' statement for best 

```python
# server
from socket import *


server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
server_addr = ('127.0.0.1', 8080)
server.bind(server_addr)
server.listen(5)

with server :
    conn, client_addr = server.accept()
    print(client_addr)
    msg = conn.recv(1024)
    conn.send(msg.upper())

new_conn, new_addr = server.accept()
```


```python
# client
from socket import *


server_addr = ('127.0.0.1', 8080)
client = create_connection(address=server_addr)


with client:
    while True:
        msg = input('>>').strip()
        if not msg: continue
        if not msg.isalpha(): continue
        client.send(msg.encode('utf-8'))
        data = client.recv(1024)
        print(data.decode('utf-8'))
        break
client.send('this msg can not be sent to the server '.encode('utf-8'))

```





```python
# 服务端结果
('127.0.0.1', 52995)
Traceback (most recent call last):
  File "D:/files/python_practice/20170828-0903/0901/server.py", line 212, in <module>
    new_conn, new_addr = server.accept()
  File "D:\Python36\lib\socket.py", line 205, in accept
    fd, addr = self._accept()
OSError: [WinError 10038] 在一个非套接字上尝试了一个操作。

Process finished with exit code 1
```


```python
# 客户端结果
>>a
A
Traceback (most recent call last):
  File "D:/files/python_practice/20170828-0903/0901/client.py", line 71, in <module>
    client.send('this msg can not be sent to the server '.encode('utf-8'))
OSError: [WinError 10038] 在一个非套接字上尝试了一个操作。

Process finished with exit code 1


```



- socket.getsockname()
	- Return the socket’s own address
	- ** useful to find out the port number of an IPv4/v6 socket, for instance**

```python
# server
from socket import *


server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
server_addr = ('127.0.0.1', 8080)
server.bind(server_addr)
server.listen(5)

with server:
    sockname = server.getsockname()
    print('server: %s - %s'%sockname)
    conn, client_addr = server.accept()
```

```python
# client
from socket import *


server_addr = ('127.0.0.1', 8080)
client = create_connection(address=server_addr)

with client:
    peername = client.getpeername()
    print(peername)
    sockname = client.getsockname()
    print('client: %s - %s'%(sockname))
```

```python
# server result
server: 127.0.0.1 - 8080

Process finished with exit code 0
```

```python
# client result
('127.0.0.1', 8080)
client: 127.0.0.1 - 53126

Process finished with exit code 0

```

- socket.gettimeout()
	- Return the timeout in seconds (float) associated with socket operations
	- return None  if  no timeout is set. 
	- **This reflects the last call to setblocking() or settimeout().**

> remote  远程的    
> interrupted    被中断  被打断   
> singal     信号   

## socket Object Methods for Client
- socket.connect(address)
	- Connect to a remote socket at address
		- thr format of address depend on the adress family
			- AF_INET      (ip, port )
	- **Note**
		1. **Blocking** waits until the connection completes instead of raising an InterruptedError（不会报错，但是会一直阻塞到链接成功，如果链接超时触发timeout）
		2. exception if the connection is interrupted by a signal, the signal handler doesn’t raise an exception and the socket is blocking or has a timeout 



- socket.getpeername()
	- Return the remote address to which the client socket is connected
	- ** useful to find out the port number of a remote IPv4/v6 socket**

- socket.getsockname()
	- Return the socket’s own address
	- ** useful to find out the port number of an IPv4/v6 socket, for instance**

```python
# server
from socket import *


server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
server_addr = ('127.0.0.1', 8080)
server.bind(server_addr)
server.listen(5)

with server:
    sockname = server.getsockname()
    print('server: %s - %s'%sockname)
    conn, client_addr = server.accept()
```

```python
# client
from socket import *


server_addr = ('127.0.0.1', 8080)
client = create_connection(address=server_addr)

with client:
    peername = client.getpeername()
    print(peername)
    sockname = client.getsockname()
    print('client: %s - %s'%(sockname))
```

```python
# server result
server: 127.0.0.1 - 8080

Process finished with exit code 0
```

```python
# client result
('127.0.0.1', 8080)
client: 127.0.0.1 - 53126

Process finished with exit code 0

```

- socket.gettimeout()
	- Return the timeout in seconds (float) associated with socket operations
	- return None  if  no timeout is set. 
	- **This reflects the last call to setblocking() or settimeout().**


- socket.listen([backlog])
	- **Enable a server to accept connections**
	- *optional parameter : backlog*
		-  If backlog is specified, it must be at least 0 (if it is lower, it is set to 0)
		-  __it specifies the number of unaccepted connections that the system will allow before refusing new connections. If not specified, a default reasonable value is chosen__
		-  注意，backlog设定的值并不影响服务端并发链接客户都按的数目
			1. 假如现在有一万台计算机像服务器主机发起了connect请求，主机不可能同时同时与所有客户成功建立起链接
			2. backlog就是控制操作系统能够在同一时间创建的链接数目，因此即使你把backlog设置为1，那么这套服务器仍然能够和许多客户端及进行通信
			3. 如果把backlog设置为0或者负数，仍然可以建立通信



## socket object common functions (server and client)
- socket.recv(bufsize[, flags])
	- Receive data from the socket
	- **data is a bytes object**
	-  bufsize is the maximum amount of data to be received at once
	-  **If the system call is interrupted and the signal handler does not raise an exception, the method now retries the system call instead of raising an InterruptedError exception**

- socket.recvfrom(bufsize[, flags])
	- return a tuple of (bytes, address )
		- bytes: a bytes object representing the data received 
		- address:  the address of the socket sending the data（发送方的(ip, port )）


- socket.send(bytes[, flags])
	- Send data to the remote_socket.
	- **Returns the number of bytes sent**
	- ** If the system call is interrupted and the signal handler does not raise an exception, the method now retries the system call instead of raising an InterruptedError exception**

```python
# 服务端
from socket import *


server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
server_addr = ('127.0.0.1', 8080)
server.bind(server_addr)
server.listen(5)


with server:
    conn, client_addr = server.accept()
    print(client_addr)
    msg = conn.recv(1024)
    msg_length = conn.send(msg.upper())
    print('send %s bytes'%msg_length)
-----------------------------------------------------------------
('127.0.0.1', 53365)
send 1 bytes

Process finished with exit code 0
```

```python
# 客户端

from socket import *


server_addr = ('127.0.0.1', 8080)
client = create_connection(address=server_addr)


with client:
    while True:
        msg = input('>>').strip()
        if not msg: continue
        if not msg.isalpha(): continue
        msg_length = client.send(msg.encode('utf-8'))
        print('send %s bytes' % msg_length)
        data = client.recv(1024)
        print(data.decode('utf-8'))
        break
------------------------------------------------------------------------------------
>>a
send 1 bytes
A

Process finished with exit code 0
```


> distination     目标    

- socket.sendto(bytes, address)
- socket.sendto(bytes, flags, address)
	- Send data to the socket
	- return the number of the bytes sent
	- **the socket should not be connected to a remote socket**
		- 这个方法的使用场景是已知目标接收端的address


- **socket.sendfile(file, offset=0, count=None)**
	- Send a file until EOF is reached 
	- **return the total number of bytes which were sent**
	- *parameters :*
		- file: must be opened in binary mode （文件必须以二进制模式打开）
		- offset: where to start reading the file
		- count: the total number of bytes to transmit until EOF is reached


- **socket.setblcoking(flag)**
	- set blocking if flasg is True      <==>   socket.settimeout(None) 
	- set nonblocking if flag is False  <==>  socket.settimeout(0.0)

> elapsed     消逝， 过去    

- **socket.settimeout(value)**
	- Set a timeout **on blocking socket operations** such as accept(), recv()
	- *parameter : value*
		- if non-zero, subsequent socket operations will raise a timeout exception if the timeout period value has elapsed before the operation has completed.
		- If None is given, the socket is put in blocking mode and __with no timeout exception__ 
		- If zero is given, the socket is put in non-blocking mode


- socket.getsockname()
	- Return the socket’s own address
	- ** useful to find out the port number of an IPv4/v6 socket, for instance**

```python
# server
from socket import *


server = socket(AF_INET, SOCK_STREAM)
server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
server_addr = ('127.0.0.1', 8080)
server.bind(server_addr)
server.listen(5)

with server:
    sockname = server.getsockname()
    print('server: %s - %s'%sockname)
    conn, client_addr = server.accept()
```

```python
# client
from socket import *


server_addr = ('127.0.0.1', 8080)
client = create_connection(address=server_addr)

with client:
    peername = client.getpeername()
    print(peername)
    sockname = client.getsockname()
    print('client: %s - %s'%(sockname))
```

```python
# server result
server: 127.0.0.1 - 8080

Process finished with exit code 0
```

```python
# client result
('127.0.0.1', 8080)
client: 127.0.0.1 - 53126

Process finished with exit code 0

```

- socket.gettimeout()
	- Return the timeout in seconds (float) associated with socket operations
	- return None  if  no timeout is set. 
	- **This reflects the last call to setblocking() or settimeout().**

## socket Object Attributes



## socket object modes and Timeout 
1. blocking
	- operations block until complete or the system returns an error (such as connection timed out).
2. non-blocking
	- operations fail if they cannot be completed immediately
	- functions from the select can be used to know when and whether a socket is available for reading or writing
3. timeout
	- operations fail if they cannot be completed within the timeout specified for the socket
		- raise a timeout exception


> internal     本质的      
> At the operating system level, sockets in timeout mode are internally set in non-blocking mode



## timeout and accept()
- If getdefaulttimeout() is not None, sockets returned by the accept() method inherit that timeout
- If getdefaulttimeout() is None
	1. if the listening socket is in blocking mode or in timeout mode, the socket returned by accept() is in blocking mode;
	2. if the listening socket is in non-blocking mode, whether the socket returned by accept() is in blocking or non-blocking mode is operating system-dependent


## Use the with statement

```python
# server
from socket import *


host = '127.0.0.1'
port = 8080
addr = (host, port)

with socket(AF_INET, SOCK_STREAM) as s:
    s.bind(addr)
    s.listen(5)
    conn, client_addr = s.accept()
    with conn:
        print('Connected by the ', client_addr)
        while True:
            msg = conn.recv(1024)
            if not msg: break
            conn.send(msg.upper())
```

```python
# client
from socket import  *


server_host = '127.0.0.1'
server_port = 8080
server_addr = (server_host, server_port)

with socket(AF_INET, SOCK_STREAM) as c:
    c.connect(server_addr)
    while True:
        msg = input('>>>').strip()
        if not msg: continue
        if not msg.isalpha():continue
        c.send(msg.encode('utf-8'))
        data = c.recv(1024)
        print(data.decode('utf-8'))
```











