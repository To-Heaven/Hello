## Git 分支
实时gengxin 两种方法


## 当前分支
#### 提交、分支、HEAD
- 每一次commit都是一次提交，对于同一个文件我们提交多次时，这些提交串成一条线，这一条线我们就成为一个分支，即分支由一次次提交构成。
- 每一个分支都有一个对应的指针，该指针指向**其自身分支下最新的提交**，默认的分支对应指针名就是master
- HEAD与分支的指针不同，一个仓库中只能有一个HEAD，它指向的是分支的指针，比如当前最新的提交是dev分支上，那么HEAD指向的就是dev分支上dev指针指向的那个提交。说的通俗点，HEAD指向的就是当前文件最新的提交



## 分支增删改查
#### 创建分支
1. git branch branch_name
	- 这个方法会创建一个新的分支
2. git checkout -b branch_name
	- 这个方法会创建一个新的分支并从当前分支切换到新的分支上

#### 切换分支
- git checkout branch_name
	- 切换到指定分支


#### 查看所有分支
- git branch
	- 显示仓库的所有分支，当前分支会用`*`标识

#### 删除分支
- git branch -d branch_name


## 合并分支
#### Fast-forward模式
1. 现在在git仓库下有一个`a.txt`文件，内容为`create a.txt`，并提交到master分支

```
$ cat a.txt
create a.txt

```

2. 创建一个分支dev，并切换到该分支
3. 在dev分支下修改文件内容如下，并提交

```
create a.txt
add a new line
```

4. 再切换回master分支，使用`git merge dev`命令将dev分支上的改变合并的master分支，此时再看master分支内容，如下

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ git merge dev
Updating 747e27a..3ae543c
Fast-forward
 a.txt | 2 ++
 1 file changed, 2 insertions(+)
 create mode 100644 a.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ cat a.txt
create a.txt
add a new line

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$

```

- 这种只在dev上做修改，而master分支上不进行修改，并将修改合并到master分支上时，Git会使用Fast-forward方式 



#### 解决冲突
- 接着上面，如果我们在dev上做了修改并提交到dev分支，切换回master分支后，我们也对`a.txt`做了修改，但是没有提交，这种情况下合并分支到master上时会报错的，我们需要将master分支上的修改提交到master分支上，才能进行合并分支。但是合并得到的分支内容会包含两部分内容，一部分是master分支上`a.txt`的内容，一个是dev分支上`a.txt`的内容

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ git checkout dev
Switched to branch 'dev'

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (dev)
$ vim a.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (dev)
$ cat a.txt
create a.txt
add a new line
ziawang

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (dev)
$ git add a.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (dev)
$ git commit -m "add ziawang"
[dev 6dc1e06] add ziawang
 1 file changed, 1 insertion(+)

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (dev)
$ git checkout master
Switched to branch 'master'

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ vim a.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ cat a.txt
create a.txt
add a new line
hello

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ git merge dev
error: Your local changes to the following files would be overwritten by merge:
        a.txt
Please commit your changes or stash them before you merge.
Aborting
Updating 3ae543c..6dc1e06

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ git add a.txt

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ git commit -m "add hello"
[master d279c7f] add hello
 1 file changed, 1 insertion(+)

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ git merge dev
Auto-merging a.txt
CONFLICT (content): Merge conflict in a.txt
Automatic merge failed; fix conflicts and then commit the result.

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master|MERGING)
$ cat a.txt
create a.txt
add a new line
<<<<<<< HEAD
hello
=======
ziawang
>>>>>>> dev

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master|MERGING)
$

```

- 在这种情况下，git提示我们当前的状态是`merging`，即正在进行合并的过程中，我们需要手动的将master分支上的修改还原才能合并
	- **如果想要取消这次合并，并取消merging状态**，使用`git merge --abort`命令

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master|MERGING)
$ git reset --hard HEAD^
HEAD is now at 3ae543c ss

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ cat a.txt
create a.txt
add a new line

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$ git merge dev
Updating 3ae543c..6dc1e06
Fast-forward
 a.txt | 1 +
 1 file changed, 1 insertion(+)

ziawa@DESKTOP-39L1EAK MINGW64 /e/learngit (master)
$

```
