# source命令


## source命令介绍

#### source的作用
- 指定指定文件中的shell命令，但是实在当前环境下执行的


#### 格式
- `source filename`
- `. filename`


#### source的用途
1. 初始化环境
2. 修改文件之后重新生效


#### source 与 `./xxx.sh`的区别
- 使用`./xxx`的时候实际上`xxx`脚本是在一个子shell中执行的，在子shell中`export 的变量`在脚本外的shell中是不存在的
- source执行该脚本的时候，不会创建一个子shell，而是在当前shell中执行的

#### 关于命令执行中的连接符`&`，`&&`, `||`
- 当多个命令使用`&`链接起来的时候，左边命令执行结束才会执行右边的命令，不管左边的命令是否成功


- 当多个命令使用`&&`链接起来的时候，命令会从左至右执行，如果某一个命令出现的错误，那么它右侧的其他命令都不会执行

```
make && make install
```

- 同理，`||`则与`&&`相反，多个命令使用`||`链接起来的时候，如果当前命令执行失败，会继续执行下一个命令，只要有一个命令执行成功了，那么后面的命令就不会再执行，否则就一直往右执行，直到命令全部执行结束



## 参考
- [source命令](https://blog.csdn.net/violet_echo_0908/article/details/52056071)
- [批处理命令](http://www.jb51.net/article/63210.htm)