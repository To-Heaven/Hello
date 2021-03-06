## VPS virtual Private Server
- VPS即虚拟专用服务器。
- VPS技术简单来说就是将一台服务器的使用资源分割成多份，由多个用户各自专享服务器的一种技术。他可以让同一台服务器上的多个VPS用户之间相互独立的使用自己的那一份服务器。

## VPS技术的实现
#### 容器技术
- 容器（虚拟机）中的每一个VPS用户都能分配到：一个独立的公网IP地址、独立的操作系统，并实现不同VPS间磁盘空间、内存、CPU资源、进程和系统配置的隔离。
- 每一个VPS主机均可独立进行重启，并拥有自己的root访问权限、用户、IP地址、内存、过程、文件、应用程序、系统函数库以及配置文件。

#### 虚拟化技术
> VPS主机是通过vmwareServer虚拟化技术实现的

- 一台主机上的多个VPS的操作系统共享操作系统内核并不相互影响，这样虚拟服务器就不需要为创建虚拟化内核耗费额外的资源
- 每一个VPS向独立的服务器一样，可以重装操作系统，在操作系统之上安装需要的软件，以及单独重启服务器。这一点优点类似我们借助virtualBox在电脑上实现的虚拟机


## VPS特点
1. 用户间彼此隔离
2. 高安全性
3. 资源控制和峰值性
	- 确保用户得到更高水平的服务和资源
4. 模板和应用程序套件
	- 及时更新操作系统和软件，降低安全风险
5. 客户端控制面板
	
6. 轻松迁移
	- 迁移时不需要停止服务
7. **备份和恢复**


## VPS的应用
#### 虚拟主机空间
- 为用户提供自由、安全可靠的虚拟主机空间、可以为个人网站提供网站空间

#### 电子商务平台

#### ASP应用平台
- ASP即active server page，动态服务端页面。是一个用来创建动态交互式网页并建立web应用程序。


#### 数据共享平台
- VPS的安全性、隔离性使得网站可以通过VPS实现数据共享、数据下载的服务

#### 数据存储平台
- 成本比独立的服务器要低。