## Django Session

#### django_session表
- 存放了客户端提交的信息和用于验证的sessionid

###### 字段
- session_key
	- 对应sessionid值
	- `iwt391t9tnrr02ibn1zjog564b7g3zmi `
- session_data
	- 客户端发送的信息(被加密)
- session_date
	- 过期时间（datatime格式）
	- `2017-11-15 07:03:05.268634 ` 

## 设置Session
#### request[key] = value

- session中key对应的value应该是客户端提交的信息（比如用户名，性别，年龄等在上下文中可能用到的数据）

#### 在配置文件中设置session
- 将key作为配置项添加到项目的settings.py文件目录下，比如`ZIAWANG = "userInfo"`
- 在`views.py`文件中需要该配置项验证session的时候，我们可以通过导入settings.py文件来调用配置项。但是要注意的是，我们应该导入`django.conf`包下的`settings.py`，，因为这个`settings.py`包含了项目名目录下的settings.py，而且通过该配置文件，我们可以获取更多的配置选项

```python
from django.conf import settings

userInfo = request.session.get(settings.ZIAWANG)

if not userInfo:
	return redirect('/login/')

```





## 查看Session
#### request.session[key]    （注意小写）
- 不推荐这种方式获取session中的值，因为当key不存在的时候，会报错。

#### request.session.get(key, default=None)
- 返回session中key对应的value，如果key不存在，就会返回default设置的值




## 删除Session
#### request.session.pop(key)
- 返回session中对应的key，并将其从session中删除

#### del request.session[key]
- 删除的是客户端保存在服务端上的信息


#### request.session.flush()
- 删除当前会话数据并删除会话cookie，删除Djangosession表中的一条记录




## session有效时间

#### 设置有效时间
- `request.session.set_expired(value)`
	- value
		- 整数： 单位为秒，表示在value秒之后session失效
		- datetime对象: 会在datetime指定时间之后失效
		- 0: 关闭浏览器之后就失效
		- None: 依赖全局的session失效策略配置

#### 清除有效时间
- `request.session.clear_expired()`




## session存储的相关配置
#### 数据库配置（默认）

```
# settings.py

SESSION_ENGINE = 'django.contrib.sessions.backends.db'   # 引擎（默认）
  
SESSION_COOKIE_NAME ＝ "sessionid"                       # Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串（默认）
SESSION_COOKIE_PATH ＝ "/"                               # Session的cookie保存的路径（默认）
SESSION_COOKIE_DOMAIN = None                             # Session的cookie保存的域名（默认）
SESSION_COOKIE_SECURE = False                            # 是否Https传输cookie（默认）
SESSION_COOKIE_HTTPONLY = True                           # 是否Session的cookie只支持http传输（默认）
SESSION_COOKIE_AGE = 1209600                             # Session的cookie失效日期（2周）（默认）
SESSION_EXPIRE_AT_BROWSER_CLOSE = False                  # 是否关闭浏览器使得Session过期（默认），优先级高于SESSION_COOKIE_AGE
SESSION_SAVE_EVERY_REQUEST = False                       # 是否每次请求都保存Session，默认修改之后才保存（默认），即每一次对网站的请求都会刷新Cookie有效时间

```

#### 缓存配置(redis)

```
## settings.py
  
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'  # 引擎
SESSION_CACHE_ALIAS = 'default'                            # 使用的缓存别名（默认内存缓存，也可以是memcache），此处别名依赖缓存的设置


SESSION_COOKIE_NAME ＝ "sessionid"                        # Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串
SESSION_COOKIE_PATH ＝ "/"                                # Session的cookie保存的路径
SESSION_COOKIE_DOMAIN = None                              # Session的cookie保存的域名
SESSION_COOKIE_SECURE = False                             # 是否Https传输cookie
SESSION_COOKIE_HTTPONLY = True                            # 是否Session的cookie只支持http传输
SESSION_COOKIE_AGE = 1209600                              # Session的cookie失效日期（2周）
SESSION_EXPIRE_AT_BROWSER_CLOSE = False                   # 是否关闭浏览器使得Session过期
SESSION_SAVE_EVERY_REQUEST = False                        # 是否每次请求都保存Session，默认修改之后才保存
```

#### 文件配置

```
# settings.py
  
SESSION_ENGINE = 'django.contrib.sessions.backends.file'    # 引擎
SESSION_FILE_PATH = None                                    # 缓存文件路径，如果为None，则使用tempfile模块获取一个临时地址tempfile.gettempdir()        
SESSION_COOKIE_NAME ＝ "sessionid"                          # Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串
SESSION_COOKIE_PATH ＝ "/"                                  # Session的cookie保存的路径
SESSION_COOKIE_DOMAIN = None                                # Session的cookie保存的域名
SESSION_COOKIE_SECURE = False                               # 是否Https传输cookie
SESSION_COOKIE_HTTPONLY = True                              # 是否Session的cookie只支持http传输
SESSION_COOKIE_AGE = 1209600                                # Session的cookie失效日期（2周）
SESSION_EXPIRE_AT_BROWSER_CLOSE = False                     # 是否关闭浏览器使得Session过期
SESSION_SAVE_EVERY_REQUEST = False                          # 是否每次请求都保存Session，默认修改之后才保存
```


#### 数据库 + 缓存配置



