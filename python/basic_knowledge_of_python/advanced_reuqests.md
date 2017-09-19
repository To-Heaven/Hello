# requests 进阶




## 注意
- 关于response对象

```python
import requests

try:
    response = requests.get('http://www.ziawang.com/status/404')
except:
    pass
if response:
    print(type(response))
------------------------------------------------
没有任何输出值
```

```
# 源码
class Response(object):
    """The :class:`Response <Response>` object, which contains a
    server's response to an HTTP request.
    """
 
    __attrs__ = [
        '_content', 'status_code', 'headers', 'url', 'history',
        'encoding', 'reason', 'cookies', 'elapsed', 'request'
    ]
 
    def __init__(self):
        super(Response, self).__init__()
 
        self._content = False
        self._content_consumed = False
 
        #: Integer Code of responded HTTP Status, e.g. 404 or 200.
        self.status_code = None
 
    def __repr__(self):
        return '<Response [%s]>' % (self.status_code)
 
    def __bool__(self):
        return self.ok
 
   @property
   def ok(self):
       try:
           self.raise_for_status()
       except HTTPError:
           return False
       return True
 
    def raise_for_status(self):
    ¦   """Raises stored :class:`HTTPError`, if one occurred."""
 
    ¦   http_error_msg = ''
 
    ¦   if 400 <= self.status_code < 500:
    ¦   ¦   http_error_msg = '%s Client Error: %s for url: %s' % (self.status_code, self.reason, self.url)
 
    ¦   elif 500 <= self.status_code < 600:
    ¦   ¦   http_error_msg = '%s Server Error: %s for url: %s' % (self.status_code, self.reason, self.url)
 
    ¦   if http_error_msg:
    ¦   ¦   raise HTTPError(http_error_msg, response=self)


```
