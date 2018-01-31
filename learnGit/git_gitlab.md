# 自己动手搭建一个gitlab

#### 步骤
1. 下载rpm安装包

```bash
curl -O https://downloads-packages.s3.amazonaws.com/centos-6.5/gitlab-7.2.1_omnibus-1.el6.x86_64.rpm
```


2. 安装ssh

```bash
yum install openssh-server
yum install postfix
service postfix start
chkconfig postfix on
```

3. 安装gitlab

```bash
rpm -i gitlab-7.2.1_omnibus-1.el6.x86_64.rpm
```

4. 配置gitlab  
	- 在`/etc/gitlab/gitlab.rb`文件中配置对外开放的域名。在配置该域名之前，确保你已经解析了该域名。

```bash
vi /etc/gitlab/gitlab.rb

# 修改 external_url 为 "http://gitlab.ziawang.com"
# 这里，我已经在阿里云上对我的`ziawang.com`域名添加了解析
```

5. 启动gitlab

```bash
gitlab-ctl reconfigure
lokkit -s http -s ssh

# 初始密码
root
5iveL!fe
```

6. 关闭gitlab

```bash
gitlab-ctl stop
```

7. 重新启动

```bash
gitlab-ctl start
```


#### 备份
###### gitlab 目录存放位置
- 默认`/var/opt/gitlab`


#### 问题
###### 1. VPS服务器ssh端口问题
- 当push到gitlab上的时候，ssh端口默认为22，但是vps虚拟机的默认端口是28137，所以我们得修改以下

- 解决方案：修改配置文件
	1. `vim /etc/gitlab/gitlab.rb`
	2. `添加如下内容`
	3. 重置配置`gitlab-ctl reconfigure`

```bash
 gitlab_rails['gitlab_shell_ssh_port'] = 28137
```

- 注意
	1. **当修改了ssh端口配置之后，在git客户端使用remote命令关联远程库的时候，需要加上端口号**


```vash
git remote add origin ssh://git@gitlab.ziawang.com:28137/ziawang/test.git
```