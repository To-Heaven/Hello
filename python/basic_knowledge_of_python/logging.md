## 参考
- standard library		   https://docs.python.org/3/library/logging.html
- basic loging tutorial	   https://docs.python.org/3/howto/logging.html#logging-basic-tutorial
- Advanced tutorial	   https://docs.python.org/3/howto/logging.html#logging-advanced-tutorial
- logging CookBook          https://docs.python.org/3/howto/logging-cookbook.html#logging-cookbook


# Logging——logging facility for python

> facility    灵活的  
> appropriate     适当的   
> destination      目的地   
> finer     好的  
> grained     出去毛的    
> 

- basic classes defined by the module
	- Logger 
		- expose the interface that application code directly uses
	- Handler
		- send the log records (created by loggers) to the appropriate destination.
	- Formatter
		- specify the layout of log records in the final output.
	-  Filter
		-  provide a finer grained facility for determining which log records to output.

> module-level     模块级别的
> period    句号   
> hierarchical     按层级划分的
> analogous      相似的        

## Logger Objects
-  Loggers are never instantiated directly, but always through the module-level function logging.getLogger(name).
	-  parameter
		-  name:__a period-separated hierarchical value__
			-  string
		-  for example  `a.b.c`
		-  give a logegr name of 'a' means that names of 'a.b', 'a.b.c'are all descendants of a
		-  you can also just give the name 'a' to a logger

> traversed      遍历   
 
-  **Note**：
	1. Multiple calls to getLogger() with the same name will always return a reference to the same Logger object.
	2. **When a logger is created, the level is set to NOTSET**
		- causes all messages to be processed when the logger is the root logger, or __delegation to the parent__ when the logger is a non-root logger. Note that the root logger is created with level WARNING.
		- ‘delegation to the parent’  meansif a logger has a level of NOTSET, its chain of ancestor loggers is traversed until either an ancestor with a level other than NOTSET is found, or the root is reached.
			1. If an ancestor is found with a level other than NOTSET, hat ancestor’s level is treated as the effective level of the logger  where the ancestor search began 
			2. If the root is reached, and it has a level of NOTSET, then all messages will be processed
		 
		


## Logger attributes
- logger.propagate
	- The constructor sets this attribute to True.
	- if True:
		-  events logged to this logger will be passed directly to the handlers of higher level (ancestor) loggers neither the level nor filters of the ancestor loggers in question are considered.
		-  如果是True，那么被logger记录的时间将被直接传递给logger的父logger及更高级别，并且不会考虑父logger及更高级别中level 和 filiter的参数设置
	- if False
		-   logging messages are not passed to the handlers of ancestor loggers.
	- **简单来说，如果值为True，时间会想父类的处理函数传送，否则就不会向上传送**   


```python


```




> threshold    门槛   
> severe      严重   
> other than    除了   
> suffix          后缀   


## levels   or     severties
- NOTSET		0
- DEBUG		10
- INFO		20
- WARNING	30
	- the default level
		- events of this level and above will be tracked, unless the logging package is configured to do otherwise
- ERROR		40
- CRITICAL	50

- how to get the level
	- for example to get the  'DEBUG' 
		- level = logging.DEBUG 

```python
import logging

print(logging.NOTSET)
print(logging.DEBUG)
print(logging.INFO)
print(logging.WARNING)
print(logging.CRITICAL)
--------------------------------------------
0
10
20
30
50

Process finished with exit code 0
```

## Logger methods
- logger.setlevel(*lvl*)
	- Sets the threshold for this logger to lvl. 
	- lvl:
		- Logging messages which are less severe than lvl will be ignored.
		- 注意上面对'When a logger is created, the level is set to NOTSET' 的介绍！！！


- Logger.isEnabledFor(lvl)
	- Indicates if a message of severity lvl would be processed by this logger

- logger.getEffectiveLevel()
	- return an integer of level if the value other than NOTSET has been set using setlevel() or traversed towards the root until a value other than NOTSET is found, and that value is returned
		- 如果logger有设置日志级别就返回，没有的话会在上一级logger实例设置中寻找
	- the return value is typically one of logging.DEBUG , and so on

```python


```



- Logger.getChild(suffix)
	- Returns a logger which is a descendant to this logger
		- 通过前缀来返回指定日志实例
	- suffix
		- string
	
	

```python
import logging

my_logger = logging.getLogger('ziawang.haha.xixi')

res1 = my_logger.getChild('haha.xixi')
res2 = my_logger.getChild('xixi')
print(res1)
print(res2)
------------------------------------------------------
<Logger ziawang.haha.xixi.haha.xixi (WARNING)>
<Logger ziawang.haha.xixi.xixi (WARNING)>

Process finished with exit code 0

```

> merge    融入   
 
- logger.debug(msg, *args, **kwargs)
- logger.info(msg, *args, **kwargs)
- logger.warning(msg, *args, **kwargs)
- logger.error(msg, *args, **kwargs)
- logger.critical(msg, *args, **kwargs)
	- Logs a message with level  on this logger
	- parameters	
		- msg:the message format string
		- args:merged into msg using the string formatting operator.
		- kwargs:
			- 固定的三种keyword参数，待补充
			- exc_info
				- default to False
				- if not False, causes exception information to be added to the logging message. If an exception tuple (in the format returned by sys.exc_info()) or an exception instance is provided, it is used

```python
import logging

my_logger = logging.getLogger('ziawang')
print(my_logger.getEffectiveLevel())

my_logger.debug('这是一个%s', 'debug msg')			# 不会输出
my_logger.info('这是一个%s', 'info msg')				# 不会输出
my_logger.warning('这是一个%s', 'warning msg')
my_logger.error('这是一个%s', 'error msg')
my_logger.critical('这是一个%s', 'critical msg')
------------------------------------
30
这是一个warning msg
这是一个error msg
这是一个critical msg

Process finished with exit code 0
```


- logger.makeRecord(name, lvl, fn, lno, msg, args, exc_info, func=None, extra=None, sinfo=None)
	- 待补充


- logger.log(lvl, msg, *args, **kwargs)
	- Logs a message with integer level lvl on this logger
	- other arguments are interpreted as for above.


- logger.exception(msg, *args, **kwargs)
	- Logs a message with level**ERROR** on this logger.

```python

```

- logger.addFilter(filt)
	- Adds the specified filter filt to this logger.

- logger.remove(filter)
	-  Removes the specified filter filt from this logger.

> consult     询问， 商量   
> in    turn   轮流     

- logger.filter(record)
	-  Applies this logger’s filters to the record and returns a true value if the record is to be processed.
		-  将logger的所有filter对record进行处理，只要有一个filter返回Flase，那么这个record就不会被logger的handler处理
	-   The filters are consulted in turn, until one of them returns a false value.
	-   If none of filters return a false value, the record will be processed (passed to handlers).If one returns a false value, no further processing of the record occurs.


- logger.addHandler(hdlr)
	- Adds the specified handler hdlr to this logger.
	
-  Logger.removeHandler(hdlr)
	-  Removes the specified handler hdlr from this logger.

- logger.handler(record)
	- Handles a record by passing it to all handlers associated with this logger
		- if   logger.propagate is True, pass the record to the logger's ancestor as well 
	- used for unpickled records received from a socket, as well as those created locally
			
- logger.hasHandlers()
	- Returns True if a handler was found, else False
	- if logger.propagate is True
		- Checks to see if this logger has any handlers configured .
	- if logger.propagate is False
		-  stops searching up the hierarchy 

- logger.findCaller(stack_info=False)
	- Finds the caller’s source filename and line number
	- return a tuple of (filename, line number, function name, stack information )
		- The stack information is returned as None unless stack_info is True.
	


## ways to be handled for events
- print the events to the console 
- write events to a disk file 
	- use  the filemode='a' to append   or  ' w' to  get the last message
		- default filemode =  'a '


## logging variable data
 
```python
>>> message = 'Note: this is a message'
>>> logging.warning(message)
WARNING:root:Note: this is a message
>>> logging.warning('%s , %s', 'note', 'this is a message')
WARNING:root:note , this is a message
>>> logging.warning('%s, %s'%('note', 'this  is a  message'))
WARNING:root:note, this  is a  message
>>> 
```

## logger objects
- how to get logger instance
	- logging.getLogger(name)
		- Note: Multiple calls to getLooger() with the same name will return a reference to the same logger object
		-  using the recommended construction logging.getLogger(__name__) is better

##  logger method
- logger.setLevel()
	- Note: when a logger is created , the level is set to NOTSET.
		- cause all messages to be processed wne the logger is root logger
	

- logger.debug(msg, *args, **kwargs)
- logger.info(msg, *args, **kwargs)
- logger.warning(msg, *args, **kwargs)
- logger.error(msg, *args, **kwargs)
- logger.critical(msg, *args, **kwargs)
	- msg is the message format string.
		- args, and kwargs merged in msg using the string formatting operator
	 

- logger.log(level, msg, *args, **kwargs)
	- logs a message with integer level 'level' on this logger 

- logger.hasHandlers()
	- this is uesed for looking for handlers in this logegr 


- logger.addHandler(hdlr)
	- add the specified handler to this logger 

- logger.removeHandler(hdlr)
	- remove the specified hdlr from this logger
	
- logger.addFilter(filt)
	- adds the specified filter filt to this logger

- logger.removefilter(filt)
	- remove the spcified filter filt to this logger



## Handler objects

- **Note**
	1.  Handler is never instantiated directly
	2.  this class acts as a base for more useful subclasses
		- StreamHandler     FileHandler     SocketHandler
	3.  the __init__() method in subclasses needs to call Handler.__init__().
		- `super().__init__()`


## Handler  Methods
- __init__(level=NOTSET)
	- Initializes the Handler instance by setting its level , default NOTSET

- setlevel(lvl)
	- Sets the threshold for this handler to lvl.
	- When a handler is created, the level is set to NOTSET (which causes all messages to be processed)

- setFormatter(form)
	- Sets the Formatter for this handler to form.

- addFilter(filt)
	- Adds the specified filter filt to this handler
	
- removeFilter(filt)
	-  Removes the specified filter filt from this handler.
	


> emit    发出  

- filter(record)
	- Applies this handler’s filters to the record and returns a true value if the record is to be processed.
		- filters are consulted in turn, until one of them returns a false value
	-  If none of them return a false value, the record will be emitted

- flush()
	- Ensure all logging output has been flushed.

> tidy up   整理  

- close()
	- Tidy up any resources used by the handler
	- 回收所有使用的资源


## StreamHandler  object
- Note:
	- StreamHandler objects sends logging output to streams such as sys.stdout, sys.stderr or any file-like object 
		- object which supports write() and flush() methods

- logging.StreamHandler(stream=None)
	- Returns a new instance of the StreamHandler class
	- stream:
		- the instance will use it for logging output, otherwise, sys.stderr will be used.

##StreamHandler object  Methods
- flush()
	- Flushes the stream by calling its flush() method
	- an explicit flush() call may be needed at times.

- emit(record)
	- write the record to the stream with a terminator
	- Note:
		- If a formatter is specified, it is used to format the record

> indefinitely    无限期地， 不定期地   
> delay          延迟   


## FileHandler Object
- logging.FileHandler(filename, mode='a', encoding=None, delay=False)
	- Returns a new instance of the FileHandler class
	- parameters	
		- If mode is not specified, 'a' is used
		- If encoding is not None, it is used to open the file with that encoding
		- If delay is true, then file opening is deferred until the first call to emit()
			- 文件延迟打开，直到调用emit()方阿飞
	- By default, the file grows indefinitely.

## FileHandler  Object Methods
- close()
	- close the file

- emit(record)
	- Ouputs the record to the file 






## Formatter Objects
-  Formatter Objects are responsible for converting a LogRecord to (usually) a string which can be interpreted by either a human or an system
-  The format string contains standard Python %-style mapping keys
-  The base Formatter allows a formatting string to be specified(*fmt*)

- logging.Formatter(fmt=None, datefmt=None. style='%')
	- Returns a new instance of the Formatter class
	- parameter：
		- The instance is initialized with a format string for the message as a whole
			- If no fmt is specified, '%(message)s' is used
		- If no datefmt is specified, the ISO8601 date format is used.
		- The style parameter can be one of ‘%’, ‘{‘ or ‘$’ 
			- merged with its data: using one of %-formatting, str.format() or string.Template


## Formatter Object Methods（待补充）
 - format(record)
- formatTime(record, datefmt=None)
- formatException(exc_info)
- formatStack(stack_info)


> sophisticated      精致的，复杂的    
> hierarchy     层次   

## Fileter Objects
- Filters can be used by Handlers and Loggers for more sophisticated filtering than is provided by levels

- logging.Filter(name='')
	- Returns an instance of the Filter class
	- If name is specified
		-  name of the  logger which, together with its children, will have its events allowed through the filter
	-   if name is a empty string
		-   allows every event.

- **Note**:
	- events which have been generated by descendant loggers will not be filtered by a logger’s filter setting  unless the filter has also been applied to those descendant loggers
		- `logger.addFilter(filt)`


> via 凭借  经过  
> wire    电报  电线   

## LogRecord Objects
- How to get the LogRecord Objects?
	1. LogRecord instances are created automatically by the Logger every time something is logged
	2. created manually via makeLogRecord()
		- from a pickled event received over the wire

- logging.LogRecord(name, level, pathname, lineno, msg, args, exc_info, func=None, sinfo=None)
	- primary information is passed in msg and args, which are combined using msg % args to create the message field of the record
	- parameters
		- name: The name of the logger used to log the event represented by this LogRecord
		- level: The numeric level of the logging event
		- pathname:The full pathname of the source file where the logging call was made.
		- lineno: The line number in the source file where the logging call was made.
		- exc_info:An exception tuple with the current exception information, or None if no exception information is available.
		- func: The name of the function or method from which the logging call was invoked.
		- sinfo:A text string representing stack information from the base of the stack in the current thread, up to the logging call.
	

## LogRecord Objects Methods 
- getMessage()
	- Returns the message for this LogRecord instance after merging any user-supplied arguments with the message
	- the value be returned always be string
		-  If the user-supplied message argument to the logging call is not a string, str() is called on it to convert it to a string.

## LogRecord Objects Attributes
- These attributes can be used to merge data from the record into the format string.
- placeholder
	- If you are using {}-formatting (str.format()), you can use {attrname} as the placeholder in the format string
	- If you are using $-formatting (string.Template), use the form ${attrname}
	- Notes:
		- replace attrname with the actual attribute name you want to use