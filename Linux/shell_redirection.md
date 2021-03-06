# shell中的重定向

### 先说说Linux的文件描述符
文件描述符是什么鬼？
- 在Python程序中，我们打开一个文件的时候会返回一个文件句柄，我们通过这个句柄来实现对文件的读写操作。当我们在Python中open打开一个文件的时候，实际上完成了两个动作：
    1. 在操作系统级别打开该文件，获取到该文件的文件描述符。操作系统操作该文件就是通过这个文件描述符来完成的
    2. Python解释器级别为我们封装了一个file-like object，通俗的说就是为我们创建了一个文件句柄，我们通过这个文件句柄来实现间接的操作磁盘中的文件。

- 上面扯了一大堆，实际上就是为了说明“文件描述符就是操作系统为了方便操作磁盘中的文件所创建的一个操作系统级别的`变量`”


默认的三个文件描述符
- Linux启动完成之后，默认的会帮助我们初始化创建三个描述符，Linux中文件描述符是用数字来表示的，Linux默认帮助我们初始化的三个文件描述符分别是`0，1，2`，他们分别对应`标准输入 standard input`、`标准输出 standard output`、`错误输出 error ouput`。

用户创建的文件描述符
- 用户一个进程的文件描述符的范围是`3~配置的最大文件打开数量`，`文件最大打开数量=ulimit -n`，我们每打开一个文件，这个文件都会有对应的描述符
- 系统最大能够打开的文件描述符数量在`/proc/sys/fs/file-max`文件中查看


### 日志重定向
> 所谓重定向，其实就是将指定结果从一个文件描述符对应的文件移动到另一个文件描述符对应的文件中

- 代码中log打的好不好直接反映了写代码人的水平，假如现在我们需要将程序中的日志信息分别记录到`.log`和`.err`文件中，`.log`中记录所有的日志输出信息，`.err`中专门记录错误日志信息
    - 以Python为例，我们可以使用logging模块的FileHandler来实现，这样我们可以通过命令行参数来指定日志文件的路径。
    - 如果不使用FileHandler，也想将程序中的日志输出到指定的文件中，我们就可以利用shell重定向来实现。这里说的详细些：
        1. 首先，程序中我们可以指定让`Error`日志输出到`sys.stderr`中，所有级别日志信息输出到`sys.stdout`中
        2. 假设这个脚本名字叫`ziawang.py`，那么在shell中我们可以这样做
            - `python ziawang.py >> ziawang.log 2>>ziawang.err`

- `>>`表示追加重定向，每一次调用脚本的时候，都会将日志信息追加到`ziawang.log`中
    - 当`ziawang.log`不存在的时候也会先创建
- `2>>`表示将error output输出重定向到`ziawang.err`中



