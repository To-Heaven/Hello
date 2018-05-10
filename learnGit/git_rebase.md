# git rebase命令

### 使用`git rebase -i`管理多个commit
- 场景
    - 现在假设有一个需求“在文件a.txt中写入一个1”，然后你开始实现需求
        1. 你首先从master分支上checkout创建了一个featur01分支 
        2. 然后在feature01分支下创建了一个文件a.txt
        3. 但是你手一抖，在文件中写入了一个2，并且你还没有看见
        4. 于是你一顿add，commit，最后push到远端的git仓库
        5. 然后老大review你的代码的时候，发现你写的是2，于是让你修改
        6. 你在本地修改好了之后，再次add、commit、push到了远端
        7. 这一次review通过了，于是你准备将最新的功能merge到master分支上
        8. 这个时候，你发现在feature01上的两个commit完全可以作为一个commit，然后再merge到master上，在master上通过git log的时候，不需要再看到你额外修改bug提交的log，而是只看到最终功能的commit信息，这个时候你就需要使用`git rebase -i`来管理这些commit了

- 场景重现
    - 这里使用git再一次重现上述流程


```
# 需求：新建文件a.txt，在a.txt中写入“1”

# 流程如下


1. 
Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (master)
$ git checkout -b feature01
Switched to a new branch 'feature01'
 
Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ vim a.txt

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ cat a.txt
2
 

2. 

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ git add a.txt

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ git commit -m "第一次提交"
[feature01 348669c] 第一次提交
 1 file changed, 1 insertion(+)
 create mode 100644 a.txt

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ git push origin feature01

3. review未通过，在本地修改a.txt，再提交到远程git仓库

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ cat a.txt
1

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ git add a.txt

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ git commit -m "第二次提交，修复bug"
[feature01 5a68c8c] 第二次提交，修复bug
 1 file changed, 1 insertion(+), 1 deletion(-)

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ git push origin feature01


4. 这个时候，我们使用git log查看一下最近的提交

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01)
$ git log
commit 5a68c8c679354656f33ed16b67d03b156810aed2 (HEAD -> feature01, origin/feature01)
Author: ZiaWang <ziawang_python@163.com>
Date:   Thu May 10 17:46:33 2018 +0800

    第二次提交，修复bug

commit 348669c81ef190705e16c31f953fdde7d33bd880
Author: ZiaWang <ziawang_python@163.com>
Date:   Thu May 10 17:45:03 2018 +0800

    第一次提交

commit 80f3b2ad9f4fa35f6b79683cf3b030660753a936 (origin/master, master)
Author: wangzihao <wangzihao@joinquant.com>
Date:   Wed May 2 16:12:29 2018 +0800

    CONTENT:写的很糟糕的msg(已修复)

```

-  高潮来了！！！在合并到master之前，我们需要考虑到一个问题，现在远程的feature01是有了这两个commit的，如果我们在本地合并了这2个commit的话，再push到远程仓库肯定会排除错误，会让我们先pull，因为合并后的远程仓库和本地仓库commit记录不一样，这个时候有两种解决方案：
    1. push的时候加上`-f`参数，强制push，但是不建议这样做
    2. 先checkout创建一个新的分支，比如`feature01-2`，在这个分支上合并这两个`commit`，最后merge的时候，也是讲这个分支上的内容merge到master上
-  这里使用第二个方案，在现实的工作中也确实使用的是这种方式。
    -  我们需要将348669c81ef和5a68c8c679354两次commit合并成一个commit，这个时候我们需要使用git rebase命令，使用这个命令的话，我们需要先找到要合并的这两个commit之前的一个commit，也就是80f3b2ad9f4，使用`git rebase -i 80f3b2ad9f4`，会进入到一个页面，内容如下

```
pick 348669c 第一次提交
pick 5a68c8c 第二次提交，修复bug

# Rebase 80f3b2a..5a68c8c onto 80f3b2a (2 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out

```

- 然后，我们将第二行的`pick`修改成`squash`
    - squash的意思是隐藏`5a68c8c`这次提交的commit，但是他的修改仍然会保存，因此意思就是将`5a68c8c`的修改合并到`348669c`上
    - 保存退出之后，会弹出一个新的界面，这个界面用来编辑commit的message
    - 内容如下

```
# This is a combination of 2 commits.
# This is the 1st commit message:

第一次提交

# This is the commit message #2:

第二次提交，修复bug

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Thu May 10 17:45:03 2018 +0800
#
# interactive rebase in progress; onto 80f3b2a
# Last commands done (2 commands done):
#    pick 348669c 第一次提交
#    squash 5a68c8c 第二次提交，修复bug
# No commands remaining.
# You are currently rebasing branch 'feature01-2' on '80f3b2a'.
#
# Changes to be committed:
#	new file:   a.txt
#

```

- 我们修改内容如下


```
# This is a combination of 2 commits.
# This is the 1st commit message:

创建a.txt，写入1

# This is the commit message #2:

# 第二次提交，修复bug

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Thu May 10 17:45:03 2018 +0800
#
# interactive rebase in progress; onto 80f3b2a
# Last commands done (2 commands done):
#    pick 348669c 第一次提交
#    squash 5a68c8c 第二次提交，修复bug
# No commands remaining.
# You are currently rebasing branch 'feature01-2' on '80f3b2a'.
#
# Changes to be committed:
#	new file:   a.txt
#
```

- 这个份时候使用`git log`查看，就会发现之前的两次commit不见了，现在合并成了一个提交，并且第二次修改有效

```
Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01-2)
$ git rebase -i 80f3b2ad9f4
[detached HEAD e1b9c60] 创建文件a.txt，写入1
 Date: Thu May 10 17:45:03 2018 +0800
 1 file changed, 1 insertion(+)
 create mode 100644 a.txt
Successfully rebased and updated refs/heads/feature01-2.

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01-2)
$ git log
commit e1b9c60d102b303b667bb0818c47882c0cb56531 (HEAD -> feature01-2)
Author: ZiaWang <ziawang_python@163.com>
Date:   Thu May 10 17:45:03 2018 +0800

    创建文件a.txt，写入1

commit 80f3b2ad9f4fa35f6b79683cf3b030660753a936 (origin/master, master)
Author: wangzihao <wangzihao@joinquant.com>
Date:   Wed May 2 16:12:29 2018 +0800

    CONTENT:写的很糟糕的msg(已修复)

commit af1ec4c701b6c9524bb60d1396df02e10307d2c4
Author: wangzihao <wangzihao@joinquant.com>
Date:   Wed May 2 16:02:34 2018 +0800

    CONTENT: 写入1，并修复bug

commit ef770af8b87b304719cc2d1aabeacf87ed13a140
Author: wangzihao <wangzihao@joinquant.com>
Date:   Wed May 2 15:50:55 2018 +0800

    CONTENT: 第二次提交


Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01-2)
$ cat a.txt
1


```

- 然后再一次执行下面步骤
    1. 将feature01-2push到远端
    2. 本地master pull下来最新的代码
    3. 切换到master上，将feature01-2合并到master上
    4. push master

```
Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01-2)
$ git push origin feature01-2
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 308 bytes | 308.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
To ssh://git.joinquant.com:23456/wangzihao/git-advance.git
 * [new branch]      feature01-2 -> feature01-2

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (feature01-2)
$ git checkout master
Switched to branch 'master'

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (master)
$ git pull --rebase origin master
remote: Counting objects: 6, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 4 (delta 2), reused 0 (delta 0)
Unpacking objects: 100% (4/4), done.
From ssh://git.joinquant.com:23456/wangzihao/git-advance
 * branch            master     -> FETCH_HEAD
   80f3b2a..e155798  master     -> origin/master
Updating 80f3b2a..e155798
Fast-forward
 monitor.txt | 20 ++++++++++++++++++++
 1 file changed, 20 insertions(+)
 create mode 100644 monitor.txt
Current branch master is up to date.

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (master)
$ git merge feature01-2 --no-ff
Merge made by the 'recursive' strategy.
 a.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 a.txt

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (master)
$ git push origin master
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 303 bytes | 303.00 KiB/s, done.
Total 2 (delta 1), reused 0 (delta 0)
To ssh://git.joinquant.com:23456/wangzihao/git-advance.git
   e155798..4193219  master -> master

Administrator@EDZ-20180330OVS MINGW64 /d/git-advanced (master)
$

```

- 大功告成！！