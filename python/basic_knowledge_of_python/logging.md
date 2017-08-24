## 参考
- standard library		   https://docs.python.org/3/library/logging.html
- basic loging tutorial	   https://docs.python.org/3/howto/logging.html#logging-basic-tutorial
- Advanced tutorial	   https://docs.python.org/3/howto/logging.html#logging-advanced-tutorial
- logging CookBook          https://docs.python.org/3/howto/logging-cookbook.html#logging-cookbook


## 单词
severity		严重  
applicability	适用
disk			磁盘
threshold	门槛
no-ops		没有行动
subsequent	随后的



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
>>> level
10
>>> type(level)
<class 'int'>
>>> 
```


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


