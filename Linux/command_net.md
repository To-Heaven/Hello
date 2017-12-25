# 网络管理命令

#### 给服务器上的登陆用户发信息 write
- 所在路径：`/usr/bin/write`
- 执行权限：所有用户
- 格式
	- `write 用户名`
		- 执行此命令之后，我们就可以编写要发送的信息了，保存信息不是用的`enter`，在这里面`enter`回变成下一行，并将当前行的信息发送给目标用户
		- 如果在信息中你输入错的内容，可以使用`Ctrl Backspace`或者Delete`键来删除错误内容


- **注意**
	1. 只能给已经登陆到这台服务器的用户发信息，可以使用`w`命令查看所有在线用户
	2. 以 " Ctrl D"保存信息结束
	3. 目标用户在bash命令行中热果然可以进行自己的任务，比如操作其他命令。当屏幕上显式`EOF`的时候，表示发送方已经`Ctrl D`结束发送信息，这个时候，接收方只需要按一下`enter`即可


```bash
[root@host ~]# write ziawang
good afternoon, ziawang
aha
thisis another message
bye~
[root@host ~]# 
```


```bash
[ziawang@host ~]$ 
Message from root@host.localdomain on pts/2 at 00:18 ...
good afternoon, ziawang
aha
ls -l
total 4
-rw-rw-r-- 1 ziawang ziawang    0 Dec 12 00:39 -bash:
drwxr-xr-x 2 root    root    4096 Dec 12 00:37 temp
-rw-rw-r-- 1 ziawang ziawang    0 Dec 12 00:39 [ziawang@host
[ziawang@host ~]$ thisis another message
bye~
EOF

[ziawang@host ~]$ 
```

####   给所有在线用户发送信息（发送广播信息）  wall
> write all   


- 所在路径：`/usr/bin/wall`
- 执行权限：所有用户
- 格式
	- `wall 信息内容`

- 注意
	- 发送信息的用户也会收到自己使用`wall`发送的信息

#### 测试网络连通性 ping
- 给远程注意发送信息包，用来踩点哈哈
- 所在路径：`/bin/ping`
- 执行权限：所有用户
- 格式
	- `ping [-c] IP地址`
		- `-c`用来指定发送次数

- 注意
	1. 不指定次数的时候，会一直ping 

```bash
[ziawang@host ~]$ ping www.google.com
PING www.google.com (216.58.219.4) 56(84) bytes of data.
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=1 ttl=54 time=0.768 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=2 ttl=54 time=0.751 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=3 ttl=54 time=0.718 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=4 ttl=54 time=0.791 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=5 ttl=54 time=0.796 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=6 ttl=54 time=0.804 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=7 ttl=54 time=1.00 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=8 ttl=54 time=0.722 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=9 ttl=54 time=1.06 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=10 ttl=54 time=1.00 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=11 ttl=54 time=0.693 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=12 ttl=54 time=3.65 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=13 ttl=54 time=0.732 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=14 ttl=54 time=0.716 ms
64 bytes from lax17s03-in-f4.1e100.net (216.58.219.4): icmp_seq=15 ttl=54 time=0.723 ms
^C
--- www.google.com ping statistics ---
15 packets transmitted, 15 received, 0% packet loss, time 14511ms
rtt min/avg/max/mdev = 0.693/0.995/3.650/0.719 ms
[ziawang@host ~]$ 
```

#### 查看和临时设置网卡信息  ifconfig
> interface configure   

- 所在路径：`/sbin/ping`
- 执行权限：root用户
- 格式
	- `ifconfig [网卡名称] IP地址`

###### eth0本地网卡, lo是回环网卡
- 本地网卡以`eth`加上数字表示，数字取决于网卡数量
- 回环网卡用`lo`表示，通常用来测试，他的IP总是固定的

```
[ziawang@host ~]$ ifconfig
eth0      Link encap:Ethernet  HWaddr AA:AA:00:0B:2B:B2  
          inet addr:172.96.203.6  Bcast:172.96.223.255  Mask:255.255.224.0
          inet6 addr: fe80::a8aa:ff:fe0b:2bb2/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:3343784 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2894517 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:4427298445 (4.1 GiB)  TX bytes:4330596933 (4.0 GiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:23792 errors:0 dropped:0 overruns:0 frame:0
          TX packets:23792 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:2269066 (2.1 MiB)  TX bytes:2269066 (2.1 MiB)

[ziawang@host ~]$ 
```

- `Link encap` 网络类型，通常都是以太网
- `HWaddr`网卡的mac地址，存放在网卡的只读存储器中
- `inet addr`当前计算机的IPV4地址
- `Bcast`当前网络的广播地址
- `Mask`子网掩码
- `inet6 addr`IPV6
- ` RX packets`接受到的数据包的总数量
- `TX packets`发送的数据包的总数量
- `RX bytes`接受的数据包的总大小
- `TX byte`发送的数据包的总大小


#### 查看发送电子邮件  mail
- 所在路径：`/bin/mail`
- 执行权限：所有用户
- 格式
	- `mail 用户名`
		- 发送邮件给指定用户
		- 使用`Ctrl D`保存退出
	- `mail`
		- 查看收到的邮件
		- 这个命令会进入一个交互界面，每一个邮件都有一个序列号，在交互界面`&`直接输入该序列号就可以查看邮件内容
			- 你也可以使用`help`查看能够使用的命令
			- `d 序列号`删除邮件
			- `h`查看邮件列表  
	
- 应用
	- 系统的报错信息、日志信息

- 注意
	- 接收方可以不在线
	- 可以给自己发送


#### 统计计算机当前和过去用户登陆信息，包括计算机重启时间  last
- 格式
	- `last`


#### 获取所有用户最后一次登录时间  lastlog
- 格式
	- `lastlog [-u 用户id]`
		- -u是只查看指定id的用户的登陆信息


```
[root@host ~]# lastlog
Username         Port     From             Latest
root             pts/4    116.117.134.73   Fri Dec 22 00:40:26 -0500 2017
bin                                        **Never logged in**
daemon                                     **Never logged in**
adm                                        **Never logged in**
lp                                         **Never logged in**
sync                                       **Never logged in**
shutdown                                   **Never logged in**
halt                                       **Never logged in**
mail                                       **Never logged in**
uucp                                       **Never logged in**
operator                                   **Never logged in**
games                                      **Never logged in**
gopher                                     **Never logged in**
ftp                                        **Never logged in**
nobody                                     **Never logged in**
vcsa                                       **Never logged in**
saslauth                                   **Never logged in**
postfix                                    **Never logged in**
sshd                                       **Never logged in**
postgres                                   **Never logged in**
ziawang          pts/5    116.117.134.73   Fri Dec 22 00:40:35 -0500 2017
dbuser                                     **Never logged in**
admin                                      **Never logged in**
openvpn                                    **Never logged in**
mysql                                      **Never logged in**
[root@host ~]# 
```

#### 显式数据包到主机之间的路径  traceroute
- 执行权限：所有用户
- 所在路径：`/bin/traceroute`
- 格式
	- `traceroute IP`

- 会返回所经过的所有结点


#### 查看网络相关信息 netstat
- 执行权限：所有用户
- 所在路径：`/bin/netstat`
- 格式
	- `netstat [-tulrn]`
		- `-t`tcp协议网络信息（传输控制协议）
		- `-u`udp协议网络信息（用户数据报协议）
		- `-l`监听listen
		- `-r`路由route
		- `-n`显示IP地址和端口号
		- `-a`所有
- 常用
	- `netstat -tlun`查看本机监听的端口
	- `netstat -an`查看本机所有网络连接，包括应用程序建立的链接
	- `netstat -rn`查看本机路由表，可以用来查询网关


#### 配置网络  setup（redhat）
- 所在路径：`/usr/bin/setup`
- 执行权限**：root**
- 格式
	- `setup`

- 注意
	- 这个命令修改的是永久的，在修改完之后需要重启网络服务
	- 这个命令只在`redhat`系列版本的Linux中存在

###### service bet restart
- 重启网络服务


#### 挂载命令  mount
- 挂载是什么意思？
	- 将我们想要使用的设备链接倒塌的挂载点上，**Linux中的挂载点是目录，在Windows中的挂载点则是一个个盘符，c, d, e, f等**
	- Linux的分区是在启动Linux的时候自动挂载的，但是对于使用的U盘，移动硬盘或者其他外连接的设备来说，不能实现自动挂载，我们需要手动挂载。
		- 简单来说，就像我们在Windows操作系统的计算机上插入了一个U盘，Windows会自动检测到该U盘并自动为其分配一个盘符，这其实就是一个自动挂载的过程

- 为什么要有挂载
	- 对于光盘等设备，在Linux上不能直接去访问其中的数据，我们需要将其挂载到一个目录下（通常为`/mnt/xxx`），挂载成功后，挂载点目录`/mnt/xxx`目录下就存放了设备的所有文件

###### 创建挂载点步骤
1. 在`/mnt`下创建一个目录`cdrom`用于挂载
2. 使用`mount`命令挂载
3. 挂在完成后，我们就可以通过挂载点目录去访问设备中的文件了



###### mount 命令
- 所在路径：`/bin/mount`
- 执行权限：所有用户
- 格式
	- `mount [-t 文件系统] 设备文件名 挂载点`
		- `-t 文件系统`指定设备的文件系统，一般系统默认为`iso9660`，所以我们可以不指定
			- 比如`iso9660`
		- `设备文件名`
			- 系统默认制定的，在Redhat系列的Linux系统中，设备文件名默认为`/dev`目录下的`sr0`，就相当于一个软连接
		- `挂载点`
			- 就是我们创建的目录
- 举例
	- `mount -t iso9660 /dev/sr0 /mnt/cdrom`
		- 或`mount /dev/sr0 /mnt/cdrom`

###### 卸载挂载点 umount
- 格式
	- `umount 设备文件名`
		- 注意，不是挂载点
- **注意**
	- 卸载完挂载点之后，再去挂载点目录下，就会发现挂载点下的设备文件都不见了
	- 你不能在挂载点目录下执行卸载该挂载点的命令，这就有点像你想用手把自己提起来一样不可能做到