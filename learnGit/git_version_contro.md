# 版本控制


## Git追踪的是修改
- Git版本控制本质上是控制和管理文件的修改。所谓修改，其实就是文件内容的增、删、改以及文件本身的创建和删除，这些在工作区的变动都会被Git追踪。



## 工作区、暂存区、分支
- 工作区就是项目下除了`.git`以外的空间，我们文件的添加、删除、修改等操作都是在工作区中进行的
- 暂存区存在于版本库`.git`中，用户修改完文件之后，使用`git add`命令将变动**添加**到暂存区
- 分支，默认分支是master，我们**添加**到暂存区中的变化需要**提交**到分支才能被其其他人从分支上看到。

## HEAD
- `HEAD`可以看作一个指针或者变量，它指向的是当前分支中的当前版本的修改
- `HEAD^` 则表示当前版本的上一个版本的修改
- 同理，`HEAD^^`表示上上一个版本的修改


## 基本操作

#### 查看版本库当前状态
- `git status	`

#### 将修改添加到暂存区
- `git add file`
	- file为项目下文件的路径

#### 将变动提交到分支
- `git commit -m "message"`
	- message是对本次修改的说明信息，每一次提交到分支上的修改都应该有message进行说明


- commit id
	- 每一次提交，Git都会为我们这次提交的修改使用`sha1`生成这个文件的一个版本id。我们可以使用这个id来操作提交的修改



#### 查看文件修改的内容
- `git diff file`
	- file为项目文件下的路径



#### 获取每一次执行过的命令
- `git reflog`


#### 显示从近到远的提交日志
- `git log`
- 显示格式
	- commit id
	- Author
	- Date
- 使用`git log --pretty=online`，将日志显示为一行
- `git log` 与 `git reflog`的区别
	- 加入西安在一个文件`a.txt`按照时间顺序他有三个版本号，分别对应`a, b, c`，当我们在`c`分支上使用`git reset --hard b`的时候，如果我们使用`git log`，我们只能得到`a，b`的版本信息，无法获取`b`之后的版本信息，但是如果我们使用`git reflog`命令，我们不但可以获取`a，b，c`所有曾经提交过的版本信息，还包括我们`HEAD指针`切换的信息

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ git reflog
6c25a1b (HEAD -> master) HEAD@{0}: reset: moving to 6c25a1
b325146 HEAD@{1}: reset: moving to b32514
6c25a1b (HEAD -> master) HEAD@{2}: reset: moving to 6c25a1b
b325146 HEAD@{3}: reset: moving to b325
6c25a1b (HEAD -> master) HEAD@{4}: commit: ADD c
b325146 HEAD@{5}: commit: add b
43b4fa5 HEAD@{6}: commit (initial): create a

ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ git log --pretty=oneline
6c25a1b2f87ef19ddf4d00508601430fb8497e5a (HEAD -> master) ADD c
b3251463935bc7fa855fd0879bbe2f243a627e8e add b
43b4fa5afd3bf4f2f3f43a451bf61e6ee51dc56e create a

```



#### 版本库软回退版本 git reset --soft 
- `soft`参数可以实现软回退，也就是说将我们提交到分支的修改回退到暂存区中的状态

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ git log --pretty=oneline
6c25a1b2f87ef19ddf4d00508601430fb8497e5a (HEAD -> master) ADD c
b3251463935bc7fa855fd0879bbe2f243a627e8e add b
43b4fa5afd3bf4f2f3f43a451bf61e6ee51dc56e create a

ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ git reset --soft HEAD^

ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   a


ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ git log --pretty=oneline
b3251463935bc7fa855fd0879bbe2f243a627e8e (HEAD -> master) add b
43b4fa5afd3bf4f2f3f43a451bf61e6ee51dc56e create a

```

#### 默认的`--mixed`参数
- `git reset [--mixed] HEAD^` 表示将当前分支上的内容回退到工作区的状态。（注意是当前分支）

``` 
ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ vim b				# 这里创建了一个名称为b的空文件

ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ git reset HEAD^
Unstaged changes after reset:
M       a

ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   a

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        b

no changes added to commit (use "git add" and/or "git commit -a")

ziawa@DESKTOP-39L1EAK MINGW64 /e/gittest (master)
$ ls
a  b

```
 
#### 还原文件内容到指定版本
- `git reset --hard HEAD^`


```bash

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ cat t.txt
this is t.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ git log
commit 79da838fa7631a3c957a261dc0396b38c1013498 (HEAD -> master)
Author: ZiaWang <ziawang_python@163.com>
Date:   Fri Nov 3 18:37:36 2017 +0800

    create t

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ git reflog
79da838 (HEAD -> master) HEAD@{0}: reset: moving to 79da838
513e3ac HEAD@{1}: reset: moving to 513e3ac
79da838 (HEAD -> master) HEAD@{2}: reset: moving to 79da838fa
513e3ac HEAD@{3}: commit: appedn a new line
79da838 (HEAD -> master) HEAD@{4}: commit (initial): create t

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ git reset --hard 513e3ac
HEAD is now at 513e3ac appedn a new line

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ cat t.txt
this is t.txt
append a new line

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$
```

#### 撤销修改
###### 撤销工作区的修改
- 文件在工作区被修改，并且没有被添加到暂存区的时候，将文件的修改撤销回被修改前的状态（上一次commit的状态）
- `git checkout -- file`（file与"--"之间有一个空格）
	- file为项目下文件的路径

###### 撤销暂存区的修改
- 文件被添加到暂存区之后，如果文件内容在工作区被在再次修改，那么下面的命令会撤销添加到暂存区之后文件在工作区中的修改
- `git checkout -- file`


###### 撤销添加到暂存区的修改
- 意思是如果将修改add到暂存区之后，突然不想提交这个修改了，将这个修改取消掉，需要两个步骤

1. `git reset HEAD file`
	- 这个命令可以将暂存区中的修改回退到工作区，但是此时文件还是被修改过的，需要第二个步骤来撤销修改
2. `git checkout -- file`
	- 这个命令将在工作区中的修改撤销

```bash
ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ git add t.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   t.txt


ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ git reset HEAD t.txt
Unstaged changes after reset:
M       t.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   t.txt

no changes added to commit (use "git add" and/or "git commit -a")

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ cat t.txt
this is t.txt
append a new line

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ git checkout t.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ git status
On branch master
nothing to commit, working tree clean

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$ cat t.txt
this is t.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/authority-management-system (master)
$

```


#### 删除文件
- 删除文件也算是一种修改，因此修改也需要提交到分支中。

###### 删除一个文件
1. `git rm file`
2. `git commit -m "remove file`

###### 撤销手动删除的文件（文件管理器）
- 所谓手动删除，其实就是在工作区中的删除
- 这种删除，我们只需要使用`git checkout -- file`即可，前提是你得记住文件名


###### 撤销暂存区中的删除
- 当我们使用`git rm file`的时候，会把对文件删除的修改自动添加到暂存区，如果这个时候想要撤销修改，需要两个步骤（其实本质上就是上面" 撤销添加到暂存区的修改"）

1. `git reset HEAD file`
	- 这一步会将暂存区中"删除"回退到工作区，但是文件在工作区仍然是删除的状态

2. `git checkout -- file`
	- 这一步会将工作区中"删除"撤销，文件就会重新回到我们的工作区中







