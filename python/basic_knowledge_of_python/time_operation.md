# python中的时间操作

#### time模块与datetime模块
python提供了time模块与datetime模块让我们处理时间，这两个模块有比较明显的区别
- time模块提供的时间处理更偏向于操作系统层面，在time模块中通常操作的对象是`timestamp时间戳对象`
- datetime模块则偏向于应用，最常使用的就是其提供的`datetime`对象，可以把它看作是对time模块的更高一级的封装
 


#### time模块与datetime模块时间的转换
- datetime模块中`datetime类`的`fromtimestamp()`方法可以将`time`模块产生的字符串转换成一个`datetime类`的实例


## time模块
#### time 模块中表示时间的几种方式
时间戳timestamp
- 从1970年1月1日00：00：00开始按秒计算的偏移量
- 获取方法
	- time.time()
- 返回时间戳的函数
	- time.time()
	- time.clock()
- 应用
	- 在文件中存时间，并且给计算机（其他程序）用的时候


struct_time（元组）
- 共有9个元素

```
   Index	Attribute                Values）
    	0	tm_year（年）	比如2011
    	1	tm_mon（月）	1 - 12
    	2	tm_mday（日）	1 - 31
    	3	tm_hour（时）	0 - 23
    	4	tm_min（分）	0 - 59
    	5	tm_sec（秒）	0 - 61
    	6	tm_wday（weekday）	0 - 6（0表示周日）
    	7	tm_yday（一年中的第几天）	1 - 366
    	8	tm_isdst（是否是夏令时）	默认为-1
```

- 返回struct_time类型对象的函数
	- time.gmtime()
	- localtime()
	- strptime()
- struct_time的元素获取可以通过命名元组的获取元素方式获取
	- 从而可以自定义时间格式

```python
time.struct_time(tm_year=2017, tm_mon=8, tm_mday=23, tm_hour=20, tm_min=28, tm_sec=37, tm_wday=2, tm_yday=235, tm_isdst=0)
235

Process finished with exit code 0

```


- 应用
	- 用于计算机对时间差进行计算（从某点时刻开始经过的时间，比较元素的值的不同）

格式化的时间字符串
- 应用
	- 需要打印/显示的时候
	- 需要记录的时候



## time.Method
- time.time()返回当前时间的时间戳 

- time.localtime([secs]) 将一个时间戳转换成**当前时区**的struct_time，secs不提供是以当前时间为准
	- secs 时间戳，可以转换指定的时间戳为struct_time

- time.gmtime([secs])将一个时间戳转换成UTC时间的struct_time

- time.mktime(struct_time) 将一个struct_time转换成时间戳方式时间

- time.sleep(secs) 线程推迟制定的时间运行（单位：秒）

- time.clock()
	- windows中，第一次调用返回进程运行的实际时间，第二次调用（之后的）是第一次开始到现在运行的时间

```python
import time

def f():
    print('开始运行。。。。time==>', time.time())

f()
print(time.clock(), time.time())
time.sleep(2)
print(time.clock(), time.time())
time.sleep(2)
print(time.clock(), time.time())
----------------------------------------------------------
开始运行。。。。  time==> 1501838444.7987068
6.034784497845582e-07 1501838444.7987068
2.0002812209575995        1501838446.7994072
4.0003777775095655      1501838448.7994757
```


- time.strftime(format[, t])：把一个代表时间的stauct_time元组或函数gtime()和localtime()的返回值转换成格式化的字符串并返回。元组中的元素越界时，会抛出ValueError错误
	- t 为struct_time类型。为指定则默认为time.localtime()

```
 格式  
 年	%Y  
 月	%m    
 日	%d  
 周	%A  
 本地相应时间  %X  
 时	%H  
 分	%M  
 秒	%S  
```

- time.strptime(string[, format]) 把一个格式化的时间转换成struct_time。是strftime()的逆操作 

```python
now = time.time()
now_local = time.localtime(now)
now_gmtime = time.gmtime(now)
now_strf = time.strftime('%Y- %m-%A- %X', now_local)
now_strp = time.strptime(now_strf, '%Y- %m-%A- %X')
print(now)
print(now_local)
print(now_gmtime)
print(now_strf)
print(now_strp)
```


- time.asctime([t]) 把一个表示时间戳或struct_time转换成字符串形式，没有参数时，默认将time.localtime()作为参数传入


![](https://raw.githubusercontent.com/ZiaWang/Hello/master/picture/time.jpg)
