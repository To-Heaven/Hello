## 为什么要用GitHub？
- **理由如下**（工科生思维   = = !）

1. 本地仓库如果一不小心被删掉或者其他原因被破坏(包括`.git`)，git再强大，也救不了你。可是如果有了GitHub，我们可以把本地仓库内容以及修改推送到GitHub上，实现本地与GitHub的同步，这样本地仓库即使被删，我们可以从GitHub上克隆出一个一样的本地仓库
2. 更好的实现项目组内人员的分工协作！ 
3. 全世界最大的同性交友网站，各种牛逼的项目等待屌丝的发掘




## ssh加密
- 我们本地创建的git仓库与远程的Github之间要想实现数据的安全传输，需要通过ssh协议来加密。因此我们需要将本地的ssh公钥交给Github，这样当我们将要上传的数据发送给Github服务器时，服务器通过我们给它的公钥就能保证：
	- 数据是从正确的本地推送到服务器的
	- 数据在推送过程中没有受到**中间人攻击**

- 关于ssh，可以看这里[《SSH与中间人攻击》]()

#### 创建ssh密钥
- `ssh-keygen`
- 执行完此命令之后，在home目录下会生成一个隐藏的`.ssh`目录，该目录下会有两个文件，`id_rsa.pub`和`id_rsa`，显然前面一个密钥就是我们要交给Github服务器的公钥，ssh私钥应保证不让别的用户或黑客获得，因此我们创建ssh过程中会有一个提示，是否要为私钥`id_rsa`设置口令，即用密码保护，如果你要上传的文件非常重要，那你应该为私钥设置一个口令。


#### https
- 除了使用ssh之外，Git还提供了https

#### 提交公钥
- 进入到个人的GitHub页面，在`settings keys`中将`id_rsa.pub`中的内容复制粘贴到页面提供的文本框中就可以啦。

#### 设置多个公钥
- 对于只有一个GitHub账号的我，如果要建立几台电脑上git仓库与同一个GitHub账号的关联，可以把每台电脑上的公钥都提交到GitHub服务器上，GitHub允许同一个用户在settings key中添加多个公钥


## 关联远程仓库
- 要想将本地git仓库与GitHub远程仓库关联，首先我们得有一个远程库。

#### 创建远程仓库
- 只要是个正常人，在GitHub上肯定能找到`create a new reposity`选项，不过要注意，本地创建的仓库名与远程仓库名最好一致，这样更方便管理

#### 关联
1. 本地gitbash下，执行命令
	- `git remote add origin git@github.com:ZiaWang/xxx.git`
	- 关于`origin`
		- origin可以看作其后路径的别名，可以不必须为"origin"，在之后对仓库第一次使用`git push`的时候，要使用这个别名


2. 推送本地内容至远程仓库
	- `git push -u origin master`
		1. 将本地仓库中master分支的内容提交到远程仓库的master
		2. 第一次推送时，将本地master分支与远程master分支关联 
	- 第一次推送数据到远程库的时候，需要加上`-u origin master`参数，这样可以讲本地master分支与远程master分支关联起来
	- 以后再提交的时候，就不用再添加`-u`参数了，只需要提供分支即可

#### ssh警告
- 第一次使用推送数据到远程仓库的时候，ssh会发出一段警告。具体解释请看[《SSH与中间人攻击》]()


## 克隆远程库
- 克隆远程仓库的命令和关联远程仓库的命令很相似，只需要将`remote add origin`换成`clone`即可

- `git clone git@github.com:ZiaWang/xxx.git`


# 合并的三种方式

## git pull
- 当本地已经有了GitHub仓库中的东西之后，如果本地域远程仓库中的内容不同（比如你在另一个主机上向仓库推送了内容），这个时候你就需要使用`git pull [ origin branch_name]` 来将GitHub上新增加的修改同步到本地。
	- 使用`git pull `命令可以让我们在多个终端上向与远程的GitHub仓库进行交互，在日常开发中，经常用到的就是这个命令 

## git fetch 
- 使用git pull 从远程GitHub上下载一个分支下来时，这个命令会做两件事
	1. 从远程GitHub上将分支拷贝到本地的分支上
	2. 再将工作区中刚下载的分支与当前分支合并，比如dev
- git featch 命令相当于完成了git pull 的第一步，当我们本地仓库的dev分支没有东西需要从远程GitHub下载分支dev内容的时候，这个命令会将远程分支拷贝到本地，并保存在`featch-dev`分支上。在这种情况下我们在工作区中是看不见分支内容的，需要我们手动合并从GitHub上featch下来的分支
	1.  `git checkout dev`确保我们在dev分之下
	2.  `git merge origin/dev`即可

## git rebase

- rebase合并的过程中，如果出现了冲突，git会停止rebase，并且等到我们手动解决冲突之后，使用git add命令，然后执行`git rebase --continue`就可以完成合并
	- 注意`git add`之后不要使用`git commit 	`

#### git rebase --abort 
- 这个参数可以用来终止rebase的命令，并恢复当前分支到rebase之前的状态

#### git rebase --continue
- 



