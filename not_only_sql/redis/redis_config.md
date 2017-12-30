# redis 的安装与配置

## 安装redis
#### Linux安装
1. 下载`wget http://download.redis.io/releases/redis-3.0.6.tar.gz`
2. 解压`tar -zxvf redis-3.0.6.tar.gz`
3. 进入解压目录执行`make`





## 配置redis

#### 默认配置
###### 端口
- redis默认端口是`6379`

#### 配置登录密码
- 在配置文件中配置密码 

```bash
requirepass your_password
```

## 启动服务与链接服务端
#### 启动服务
- 启动服务有几种方式

###### 1. 直接启动
- 直接启动前你需要进入redis的安装目录的src下，或者使用绝对路径
	- `/ziawang/redis-3.0.6/src/redis-server`
	- 或者进入src目录下直接启动`redis-server`

###### 2. 指定配置文件启动
- 启动redis服务端的时候可以指定配置文件
	- `src/redis-server redis.conf`

