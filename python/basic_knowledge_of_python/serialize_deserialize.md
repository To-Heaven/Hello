## 序列化与反序列化的应用

- 保存程序某个时刻运行的状态
- 分布式系统/多用户中数据结构(对象，程序等)的互相传输

> 序列化即把内存中的数据结构对象在不丢失其状态的情况下转换成对象的文本或二进制格式。反序列化可以将这种文本/二进制格式还原成相同状态的数据结构对象



## pickle与cPickle
> [pickle模块介绍](http://www.ziawang.com/python/basic_knowledge_of_python/pickle.html)   
> python3中的pickle就是python2中的cPickle

- cPickle(python2)是c语言实现方式的pickle模块，其转换效率远比pickle模块高，而且其提供的interface接口和pickle完全相同，只需要在导入的时候给其娶一个别名就可以和使用pickle模块一样使用cPickle模块了
	- `import cPickle as pickle`
	- 但是**cPickle模块不支持继承**，因此不能通过继承cPickle来创建子类派生其功能，而且也没必要继承。创建子类时，只需要在子类方法中调用其功能接口即可


- pickle的优缺点
	- **缺点**
		- 不能保证数据结构对象的安全性！因此，必须从一个可靠的来源获取序列化之后的对象
		- 不能保证原子性
			- 所谓原子性就是任务完成的一致性，只要其中一个子任务失败，那么任务下的所有子任务全部回滚到任务开始前的状态
			- pickle不能保证原子性。如果同一个线程/进程中多个数据中的某一个数据在pickle序列化的时候出错，在其之前完成的序列化对象不会消失
		- 不能跨语言，不同语言间难以兼容
			- pickle只能序列化python中的对象，对于其他语言中的对象，pickle不能序列化 
	- **优点**	
		- 可以跨平台
			- 对于分布式/多用户来说，pickle序列化的数据结构对象可以在不同操作系统上被反序列化
		- 支持python中大部分对象
		- 可扩展
		
- 注意
	- **pickle操作的文件必须以二进制模式打开**，因为pickle把数据结构对象序列化成一个二进制存放在文件中 
	- 不可被序列化的对象：socket套接字、文件句柄、connection(pyMysql)
		- 要想实现对上述对象的序列化，我们可以扩展pickle的功能
		- 但是要解决一个重要问题
			- 保存被序列化的对象的状态，并且在该对象被反序列化的时候，仍然是序列化时的状态。这就需要通过"__getstat__()"和"__setstat__()"来实现
		- 以文件句柄为例
		
```python
import pickle


class TextReader:
    def __init__(self, filename):
        self.filename = filename
        self.file = open(filename)
        self.position = self.file.tell()

    def readline(self):
        line = self.file.readline()
        self.position = self.file.tell()
        if not line:
            return None
        if line.endswith('\n'):
            line = line[:-1]
        return "{} : {}".format(self.position, line)

    def __getstate__(self):
        state = self.__dict__.copy()
        del state['file']
        return state

    def __setstate__(self, state):
        self.__dict__.update(state)
        file = open(self.filename)
        self.file = file


reader = TextReader("pickle_fileobject_test")
print(reader.readline())
print(reader.readline())
print(reader.readline())
obj_bytes = pickle.dumps(reader)
new_reader = pickle.loads(obj_bytes)
print(new_reader.readline())
# 虽然能够序列化file object了，但是此程序还不能保存文件的状态（光标位置）
```
 

## json