# 使用supervisor管理多个进程

#### supervisor是什么？为什么要使用supervisor？
- 但我们在一台机器上启动一个进程对外提供服务后，比如我们启动一个Web应用来对外提供Web服务，在一些情况下该进程可能会终止或者被killed，比如服务器宕机或者改进程占用磁盘or内存过高的时候，可能会被我们预先安排的监控脚本kill并清理。当提供Web应用的进程终止之后，我们要向它继续对外提供服务，就必须重启该进程
- 但是问题来了，我们不可能做到眼睛一直盯着这个进程，应该有一个监控程序自动帮助我们去监控这个进程，一旦该进程终止了，这个监控程序就会帮助我们立即重新启动该进程，保证对外及时的提供Web服务。supervisor就是帮助我们完成这个工作的工具。

#### 如何使用supervisor？
两个部分
1. 配置
2. 命令


## 使用supervisor
#### 配置
先来看完整supervisor配置

```
[unix_http_server]
file=/var/run/supervisor.sock   # supervisorctl与supervisord进行通信的socket文件
chmod=0666                      # socket文件权限

[supervisord]                   # 服务端相关配置
logfile=/var/log/supervisor/supervisord.log # 日志路径
pidfile=/var/run/supervisord.pid # supervisor 进程pid文件
childlogdir=/var/log/supervisor            ; ('AUTO' child log dir, default $TEMP)

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///var/run/supervisor.sock 

[include]           # include指向的是存放了supervisor要管理的子进程的配置文件路径
files = /etc/supervisor/conf.d/*.conf
```


子进程配置
- supervisor管理进程的方式是这样的
    1. supervisor通过Linux的fork 或者 exec来启动一个子进程，并且该子进程是一个守护进程
    2. supervisor通过监控开启的子进程来管理这些子进程，比如关闭、启动、重启等

- 子进程的对应的配置文件要以`conf`结尾，并且该文件中必须有一个section为`program:xxxx`，xxx代表进程名称

```
[program:uwsgi]
user=xxx        # 是用哪个用户执行命令
directory=xxxx  # 进程的启动目录，可以没有
command=xxxx    # 启动进程时执行的命令
autorestart=True    # 程序异常终止后自动重新启动
autostart=True  # supervisor启动的时候，对应的子程序也重新启动
startsecs=10
startretries=3  # 启动进程失败后的默认重启次数
stopwaitsecs=10 
environment=PATH=xxx    # 添加需要的环境变量

# 关于stopwaitsecs参数
- SIGCHLD：Linux中子进程在终止之后，会向父进程发送一个SIGCHLD参数
- 当supervisor向操作系统发送一个stopsignal的时候，会等待接收子进程终止之后操作系统返回的SIGCHLD信号，如果在等待接收该信号的时间大于了stopwaitsecs，supervisor就会使用SIGKILL来终止该子进程

# 关于startsecs参数
- 在supervisor启动一个子进程之后，子进程一开始会是STARTING状态，在等待了startsecs之后，如果子进程仍然处于STARTING状态，supervisor才会认为该子进程启动成功了，并且会将该子进程的状态从STARTING切换到RUNNING
```

#### 命令
1. 启动supervisord服务进程
    - `supervisord -c /etc/supervisord.conf`
    - `-c`表示指定配置文件

2. 启动某个进程
    - `supervisorctl start xxx`

3. 重启某一个进程
    - `supervisorctl restart xxx`

4. 停止某一个进程
    - `supervisorctl stop xxx`

5. 停止所有进程
    - `supervisorctl stop all`

6. 重新载入所有配置文件并重启所有进程（按照新的配置），此命令一般很少用
    - `supervisorctl reload`

7. **读取对配置文件的修改**
    - `supervisorctl reread`

8. **更新新的配置到supervisord，不影响已经处于运行状态的子进程**
    - `supervisorctl update`

9. 停止所有属于名为xxx分组的进程（对应的也有start、restart）
    - `supervisorctl stop xxx`

10. 进入supervisor的shell界面执行上述命令（此时下面的命令不需要带`supervisorctl `）
    - `supervisorctl -c /etc/supervisord.conf`