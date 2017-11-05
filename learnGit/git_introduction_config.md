## Git 介绍

#### 集中式版本控制系统与分布式版本控制系统
###### 集中式版本控制系统(SVN)
- 版本库存放在中央服务器中，在集中式版本控制环境下进行工作时，必须先从中央服务器上获取数据，当工作完毕之后再将数据提交到中央服务器。
- 缺点
	- 需要通过互联网才能获取中央服务器中版本库的内容
	- **待补充**
###### 分布式版本控制系统(Git)
- 每一台计算机中都会有一个完整的版本库，不需要链接互联网就能实现版本控制


## Git 安装
- linux
	- ubuntu: `sudo apt-get install git`

- windows
	-  使用`msgit`

## 创建git仓库
- 每一个创建的git仓库(版本库)都有一个隐藏的`.git`文件夹，该文件夹下存放的是版本库的暂存区、分支等相关配置
- 步骤
	1. 切换项目根目录下
	2. `git init`


## 版本库配置
###### 全局配置
- 将计算机下创建的所有版本库指定同一个user
	1. `git config --global user.name "ziawang"`
	2. `git config --global user.email "ziawnag_pyhon@163.com"`

###### 非全局配置
- 为单个版本库指定user
	1. 首先进入项目根目录下 
	2. `git config user.name "ziawang"`
	3. `git config user.email "ziawang_python@163.com"`
	
###### 查看当前配置
- `git config --list`


```
ziawa@DESKTOP-39L1EAK MINGW64 ~
$ git config --list
core.symlinks=false
core.autocrlf=true
core.fscache=true
color.diff=auto
color.status=auto
color.branch=auto
color.interactive=true
help.format=html
rebase.autosquash=true
http.sslcainfo=D:/Git/mingw64/ssl/certs/ca-bundle.crt
diff.astextplain.textconv=astextplain
filter.lfs.clean=git-lfs clean -- %f
filter.lfs.smudge=git-lfs smudge -- %f
filter.lfs.required=true
filter.lfs.process=git-lfs filter-process
credential.helper=manager
user.name=ZiaWang								
user.email=ziawang_python@163.com

ziawa@DESKTOP-39L1EAK MINGW64 ~

```