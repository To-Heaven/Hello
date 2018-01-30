# ICMP协议(internet control message protocol)


#### ICMP是什么？
- 英文翻译叫"网络控制消息协议"，位于TCP/IP模型的第三层`网络层`，它依靠IP来完成通信过程中各种问题的反馈信息，通过ICMP我们有针对性的对不同的问题采用不同的解决方案。 所以呢，我们可以把ICMP看作**提供网络故障问题反馈信息的机制**

#### ICMP特点
> 总结自[维基百科:"互联网控制消息协议"](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91%E6%8E%A7%E5%88%B6%E6%B6%88%E6%81%AF%E5%8D%8F%E8%AE%AE)

1. 直接封装在IP数据包中（可UDP一样是不可靠的协议）
	- ICMP报头封装在IP包头中 
2. 处理ICMP消息的时候和一般数据包的处理不同，往往需要查看ICMP消息的内容，获取消息中包含的详细错误类型之后，再将错误信息或者错误的`type`和`code`转发给产生IP数据包的程序

#### 结构
###### header报头和data数据
- header
	- ICMP报头包含在IP报头中，在IP报头的第20字节（第160bit位）开始，总共占用**4字节**

```
160-167    Type（1字节，错误类型）
168-175    Code（1字节，进一步划分错误类型）
176-191    Checksum校验码（2字节，包含检查错误的数据）

```

- data
	- 和大部分协议一样，报文data部分紧贴在header之后
	- ICMP的data以8bit位位一组


###### Type和Code详细
- 这种设计让我想起了HTTP协议中的`4xx`, `2xx`, `3xx`, `5xx`，HTTP协议中将不同类型的错误信息以不同的数字开头，比如`5xx`代表服务端错误、`3xx`代表重定向，而后面的数字就是**为了对错误的类型进行更细致的划分**，比如`301`代表永久重定向,`302`代表暂时性重定向。

- ICMP协议中，type和code使用起来思路和上述描述相同。`Type`用来表示生成错误的类型，`Code`则是对错误类型更细致的划分，这里引用"维基百科"中的表格，常见的错误类型type和错误更细致的划分code如下

```
0    echo reply（还没搞懂这个什么意思，但是目前明确的是，这个type是被"ping"命令调用的）

 

 
type: 3 - 目的不可达	
对应code如下:
	0		目标网络不可达		
	1		目标主机不可达		
	2		目标协议不可达		
	3		目标端口不可达		
	4		要求分段并设置DF flag标志		
	5		源路由失败		
	6		未知的目标网络	
	7		未知的目标主机		
	8		源主机隔离（作废不用）		
	9		禁止访问的网络		
	10		禁止访问的主机		
	11		对特定的TOS 网络不可达		
	12		对特定的TOS 主机不可达		
	13		由于过滤 网络流量被禁止	
	14		主机越权		
	15		优先权终止生效


type: 5 - 重定向	
对应code如下:
	0		重定向网络		 
	1		重定向主机		 
	2		基于TOS 的网络重定向		 
	3		基于TOS 的主机重定向


type: 8 - 请求回显
对应code如下:
	0		echo request


type: 9 - 路由器通告
对应code如下:
	0		路由通告


type: 10 - 路由器请求
对应code如下:
	0		路由器的发现/选择/请求

type: 11 - ICMP 超时
对应code如下：
	0		TTL 超时		 
	1		分片重组超时

type: 12 - 参数问题：错误IP头部
对应code如下：
	0		IP 报首部参数错误		 
	1		丢失必要选项		 
	2		不支持的长度

type: 13 - 时间戳请求
对应code如下：
	0		时间戳请求

type: 14 - 时间戳应答
对应code如下：
	0		时间戳应答
```


- 差错报告报文
	- `3 5 11 12 `

- 询问报文
	- `8或0`，会送请求和回答。`ping`工具就是利用这个实现的
		- 发送端发送`ICMP回送请求消息`给目标主机，目标主机**必须返回`ICMP会送应答消息`给发送端**。注意这里的`ICMP回送请求消息`和`ICMP回送应答消息`
	- `13或14`，时间戳请求和回答





#### 作用与功能
> 我认为ICMP最最重要的意义就是对不同类型的错误进行了分类并且更细致的划分。通过这些错误信息，我们可以有效的对不同错误类型做出不同的应对方案。

1. 侦测远端目标主机是否存在
	- 比如`ping, traceroute` 
2. 建立及维护路由资料
	- 利用类型3， 9， 10， 11
3. 重导资料传送路径
	- 利用类型3
4. 资料流量控制
	- 利用类型3

#### 基于ICMP的工具
###### ping及其原理
- 先看看举例

```bash
C:\Users\ziawa>ping www.ziawang.com

正在 Ping www.ziawang.com [172.96.203.6] 具有 32 字节的数据:
来自 172.96.203.6 的回复: 字节=32 时间=285ms TTL=45
来自 172.96.203.6 的回复: 字节=32 时间=294ms TTL=45
来自 172.96.203.6 的回复: 字节=32 时间=283ms TTL=45
来自 172.96.203.6 的回复: 字节=32 时间=288ms TTL=45

172.96.203.6 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 283ms，最长 = 294ms，平均 = 287ms

C:\Users\ziawa>
```

- 上面例子中，我是在本地Windows命令行中，执行了`ping www.ziawang.com`操作，可以看出`www.ziawang.com`解析后的`IP地址为172.96.203.6`，我本地计算机的`IP地址为192.168.20.50`

**那么上述执行ping命令，完整工作流程是这样**
1. 在本地，`ping`命令会构建一个固定格式的`ICMP请求数据包`（ICMP依赖于IP协议并且封装在IP协议）
2. 将该`ICMP数据包`和目标IP地址`172.96.203.6`异同交给`IP协议`，`IP`协议将`172.96.203.6`作为目的地址，本地主机`192.168.20.50`作为源地址，构成一个`IP数据包`
3. 在本地主机的`ARP缓存表`中寻找`172.96.203.6`对应的`mac地址`，并将该`mac地址一并交给`数据链路层，如果在本地`arp缓存表`中没有找到目标主机`IP`对应的`mac地址`，就会通过`ARP协议获取局域网中路由的mac地址`，具体实现可以查看我的另一篇文章[ARP协议工作流程总结](http://www.ziawang.com/article/211/)
4. 在`数据链路层`中，会按照`以太网协议`构造一个`数据帧`，该数据帧的报头中存放的是`本地主机的mac地址`和`目标主机的mac`地址。
5. 最后经由`物理层`将数据包发送给`172.96.203.6`远端主机。
6. 目标主机接收到这个数据包之后，路程是这样
	1. `物理层`会接收到这一段`二进制流`，经过`数据链路层`，按照`以太网协议`的规则解析之后，获取数据帧中`目标mac地址`，如果`目标mac地址`和自己的`mac地址`相同，就会接收该数据包，否则就丢弃该数据包。
	2. 接收到该数据包之后，会对其进行层层拆解，在`网络层`会获取到`IP协议`封装的`数据包`，`网络层`会根据`IP协议`的封装规则对`IP数据包`进行解析，如果在`160bit位`位置开始发现了`ICMP协议i数据包`，`网络层`处理该数据包之后就会立马构建一个`ICMP应答包`，该包中封装了`ICMP回送应答消息`。
	3. 接着将封装好的`ICMP应答包`经`网络层`、`数据链路层`、`物理层`再次层层打包之后发给最初发送`ICMP请求包`的`192.168.20.50`本地主机。


###### traceroute
traceroute与ping的区别？
- ping工具的一个缺点就是**由于IP头的闲置，ping工具不能完全的将数据包经过的路径（路由器）记录下来**，`traceroute`就可以帮助我们你不这个遗憾


引子————TTL
- `TTL`即`time to live`，即生存时间。它表示指定的`IP包`在被路由器丢弃之前允许通过的最大网段数量（即经过的路由数量）
	- 说白了，TTL就是用来闲置一个数据包在网络传输过程中能够存在的时间，这个时间是以`经过的路由个数进行计算的`
	- 当`IP数据包`在网络中进行传递的时候，路由器一般都会将`IP数据包中的TTL值减去1`，当路由器接收到一个`TTL=0`的数据包时，就会将该数据包丢弃，并通过`ICMP协议`将`包含"ICMP 超时-TTL超时"对应的type和code`的`反馈信息`返回发送方

- 作用
	- 避免IP包在网络中的**无线循环和收发**，节省了网络资源，并可以通过`ICMP协议`向`IP包的发送者`发送警告信息

- 设置
	- `TTL`值**由发送方设置**，防止`IP包`在互联网中永不终止的循环。
	- **路由器转发`IP数据包`的时候，要求至少将`TTL`的值减去1**

- 应用 	
	- traceroute工具


traceroute原理流程详细分析（设计的非常巧妙）
0. 先放我的测试
	- 测试环境，`centos6`
	- 本地主机IP: `172.96.203.6`
	- 目标主机域名：`www.amazon.com`

```
[root@host ziawang]# traceroute www.amazon.com
traceroute to www.amazon.com (23.74.61.104), 30 hops max, 60 byte packets
 1  * * *
 2  192.254.81.176 (192.254.81.176)  6.481 ms  6.740 ms  6.189 ms
 3  192.254.81.60 (192.254.81.60)  1.334 ms  1.322 ms  1.297 ms
 4  ix-xe-7-2-4-0.tcore1.LVW-Los-Angeles.as6453.net (66.110.59.193)  0.489 ms  0.481 ms ix-ge-1-0-0.mcore3.LAA-Los-Angeles.as6453.net (209.58.85.57)  0.455 ms
 5  if-xe-1-0-3-12.tcore1.LVW-Los-Angeles.as6453.net (66.110.59.53)  35.648 ms  35.650 ms  35.641 ms
 6  if-ae-20-2.tcore1.00S-Seattle.as6453.net (64.86.123.94)  35.632 ms  35.159 ms  35.134 ms
 7  64.86.123.54 (64.86.123.54)  31.071 ms  32.115 ms  47.200 ms
 8  64.86.123.54 (64.86.123.54)  31.730 ms 205.185.218.74 (205.185.218.74)  31.431 ms  32.398 ms
 9  205.185.218.74 (205.185.218.74)  32.087 ms a23-74-61-104.deploy.static.akamaitechnologies.com (23.74.61.104)  31.625 ms  32.712 ms
[root@host ziawang]# 
```

1. 首先，`本地主机`会给目标主机发送一个`TTL=1`的`UDP数据包`，当经过第一个路由器的时候，这个`TTL`值就会`减1`，意味着下一个路由接收到这个`UDP包`的时候发现`TTL=0`，于是就将该数据包丢弃，并**产生一个"主机不可达的`ICMP数据包`"给本地主机**，本地主机收到包含这个`ICMP数据`的包之后，就会再发一个`TTL=2`的`UDP数据包`给目标主机，重复上述步骤，然后在到达第三个路由器的时候，`TTL值再次变成0`，于是这台路由器在丢弃这个数据包之后，同返回一个**"主机不可达的`ICMP数据包`"给本地主机**，本地主机收到这个包之后，再发送一个`TTL=3`的数据包给目标主机.....，直到数据包被目标主机接收到。
2. 经过上述不断重复的过程，目标主机总算收到了这个数据包，然后目标主机就会封装一个`ICMP端口不可达的数据包`给本地主机（tracroute发送的端口号>30000，一般主机不会用>30000的端口来监听UDP请求），当本地主机接收到这个包，发现里面的`ICMP信息`之后，就直到这个包已经到达了目标主机。







