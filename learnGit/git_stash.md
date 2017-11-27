# 使用git stash保留工作现场


## 使用背景
- 为什么要保留工作现场呢，假设你现在在夏目下开发一个新的应用，而组长突然给你打了个电话，告诉你现在项目下的一个应用出现了bug，需要赶紧修复，而且你手头上正在进行着的应用显然不能立刻提交的，这就需要我们将当前开发的项目的工作环境打包保存起来，修复完bug之后我们再回来继续开发。
- 上面就是在开发中经常用到的分支管理策略

## 实例
1. 当前master分支下有一个存在bug的文件a，我们当前所在的开发分支为dev，内容和状态如下

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (master)
$ ls
a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (master)
$ cat a
create file named a
ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (master)
$ git checkout -b dev
Switched to a new branch 'dev'

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev)
$ vim a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev)
$ cat a
create file named a
dev branch developent

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev)
$ git status
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   a

no changes added to commit (use "git add" and/or "git commit -a")

```

2. 现在dev上的内容没有提交的情况下将使用git stash保存工作现场

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev)
$ git stash
warning: LF will be replaced by CRLF in a.
The file will have its original line endings in your working directory.
Saved working directory and index state WIP on dev: 3159ead CREATE: create a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev)
$ git stash list
stash@{0}: WIP on dev: 3159ead CREATE: create a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev)
$
```

3. 切换到master上并创建新的bug_001分支，修复bug并提交到bug_001分支

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (master)
$ git checkout -b dev_001
Switched to a new branch 'dev_001'

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ ls
a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ vim a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ cat a
create a file named a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git add a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git commit -m "DEBUG: debug a for missing 'a'"
[dev_001 ed23100] DEBUG: debug a for missing 'a'
 1 file changed, 1 insertion(+), 1 deletion(-)

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$

```

4. 切换回master分支，并合并bug_001

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (master)
$ git merge --no-ff -m "MERGE: merge bug001 to master " dev_001
Merge made by the 'recursive' strategy.
 a | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (master)
$ cat a
create a file named a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (master)
$

```


5. 此时回到我们开发的应用分支dev下，由于master分支上提交了修改，回合dev产生冲突，所以在恢复现场之前，我们先在dev分支上合并一下master上的修改

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (master)
$ git checkout dev_001
Switched to branch 'dev_001'

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git merge --no-ff -m "MERGE: merge bug001 with master" master
Already up-to-date!
Merge made by the 'recursive' strategy.

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ cat a
create a file named a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$

```

6. 合并完之后，我们可以使用以下命令配合恢复现场
	1. git stash list 查看当前分支中保存的现场
	2. git stash apply stash@{n}:  恢复现场
	3. git stash pop stash@{n}: 恢复现场并删除该现场
	4. git stash drop stash@{n}: 删除改现场


```
ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git stash list
stash@{0}: WIP on dev: 3159ead CREATE: create a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ ^C

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git stash pop stash@{0}
Auto-merging a
CONFLICT (content): Merge conflict in a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ cat a
<<<<<<< Updated upstream
create a file named a
=======
create file named a
dev branch developent
>>>>>>> Stashed changes


```

7. 看提示，上面说内容有冲突，这个时候吗我们需要手动修改

```
ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ vim a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git status
On branch dev_001
Unmerged paths:
  (use "git reset HEAD <file>..." to unstage)
  (use "git add <file>..." to mark resolution)

        both modified:   a

no changes added to commit (use "git add" and/or "git commit -a")

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ cat a
create a file named a
dev branch developent

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git status
On branch dev_001
Unmerged paths:
  (use "git reset HEAD <file>..." to unstage)
  (use "git add <file>..." to mark resolution)

        both modified:   a

no changes added to commit (use "git add" and/or "git commit -a")

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git add a

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git commit -m "MODIFY: merge bug"
[dev_001 d075f82] MODIFY: merge bug
 1 file changed, 1 insertion(+)

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$ git status
On branch dev_001
nothing to commit, working tree clean

ziawa@DESKTOP-39L1EAK MINGW64 /e/use_stash (dev_001)
$

```