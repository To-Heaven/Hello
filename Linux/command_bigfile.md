# linux下生成大文件命令
> 使用场景：测试


## dd命令
#### 介绍
常用格式
- `dd if=源文件路径 of=目标文件路径 ibs=要从源文件中读取的数据的大小 count=该数据要在目标文件中拷贝的次数`

- 参数
	- if：源文件路径，在测试时，一般使用`/dev/zero`
	- of：目标文件路径，从源文件中读取的数据将会写入到目标文件中
	- ibs：从源文件中要读取的数据大小，比如`10M, 1G`
	- count：当指定count选项的时候，表示将ibs大小的数据拷贝指定次数并写入到of对应的目标文件
	- conv：用来转换数据的选项，常以下几种用的值有
		- `ucase`，将小写字母转换成大写字母
		- `lcate`，将大写字母转换成小写字母



#### 使用
- 在一些安全测试中，测试用户能够在进程中使用的磁盘大小，我们就可以以用户身份去执行`dd`命令创建大文件。
	- 比如，测试在`/tmp/file/`目录下创建一个大小为1000M的文件，我们以`/dev/zero`作为源文件

```bash
[root@iZj6cf9m2ds3r922wb61hqZ ziawang]# mkdir /tmp/file
[root@iZj6cf9m2ds3r922wb61hqZ ziawang]# dd if=/dev/zero of=/tmp/file/a.txt ibs=100M count=10
10+0 records in
2048000+0 records out
1048576000 bytes (1.0 GB) copied, 17.3417 s, 60.5 MB/s
[root@iZj6cf9m2ds3r922wb61hqZ ziawang]# ls -lh /tmp/file/
total 1001M
-rw-r--r-- 1 root root 1000M Apr 13 15:58 a.txt
[root@iZj6cf9m2ds3r922wb61hqZ ziawang]# 

```

## fallocate 命令
- fallocate命令也可以创建一个指定大小的文件，它使用起来要比`dd`更方便快速

```bash
[root@iZj6cf9m2ds3r922wb61hqZ ziawang]# fallocate -l 100M /home/wangzihao/xxx.abc
[root@iZj6cf9m2ds3r922wb61hqZ ziawang]# cd ../wangzihao/
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# ls
xxx.abc
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# ls -l
total 102400
-rw-r--r-- 1 root root 104857600 Apr 13 16:19 xxx.abc
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# ls -lh
total 100M
-rw-r--r-- 1 root root 100M Apr 13 16:19 xxx.abc
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# 

```


## truncate命令
- truncate可以将指定的文件缩短或者扩展为指定大小

```bash
# 这里我们将上面100m的xxx.abc缩短为10M大小，再增大为1G大小

[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# ls
xxx.abc
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# truncate -s 10M xxx.abc 
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# ls -l
total 10240
-rw-r--r-- 1 root root 10485760 Apr 13 16:23 xxx.abc
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# ls -lh
total 10M
-rw-r--r-- 1 root root 10M Apr 13 16:23 xxx.abc
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# truncate -s 1G xxx.abc 
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# ls -lh
total 10M
-rw-r--r-- 1 root root 1.0G Apr 13 16:24 xxx.abc
[root@iZj6cf9m2ds3r922wb61hqZ wangzihao]# 

```

