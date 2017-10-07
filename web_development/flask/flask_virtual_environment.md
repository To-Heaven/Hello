## virtualenv介绍
- virtualenv用来给项目创建虚拟的python解释器环境，可以避免不同项目间同时使用一个python解释器环境而出现冲突
	- 比如包的版本冲突
		- 有的项目需要最新的Flask版本，而有的项目是基于旧版本开展的，因此为了保证不同项目之间对同种包不同版本的需求，使用虚拟环境是一个不错的选择


## 为项目创建虚拟环境的好处
- 可以让每一个项目单独享用虚拟的python解释器环境，隔离不同项目之间对不同版本的依赖
- 不需要管理员权限
	- 为什么不需要？待补充

## 安装和配置
- 直接使用python的pip工具来安装virtualenv

```dos
# 如果你是python3
pip3 install virtualenv

# 如果你是python2
pip2 install virtualenv
```

## 创建虚拟环境
- 进入项目文件夹下，使用`virtualenv 虚拟环境名称`，就可以将当前项目内的脚本在虚拟环境中运行了

```dos
# 通常虚拟环境名称为"venv"
virtualenv venv	
```

## 启动（激活）/关闭虚拟环境
- 激活了项目目录下的虚拟环境后，项目才能在虚拟环境中运行
- 进入项目目录下，再使用以下命令激活虚拟空间

```dos
# linux
source venv/bin/activate
# windows
\venv\Scripts\activate
```

- 使用以下命令关闭虚拟环境
	- **虚拟环境在每一次终端（命令行）关闭时，都会自动关闭**

```dos
deactivate
```