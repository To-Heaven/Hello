# 定义子类必须实现的方法

#### 背景
- 在工厂模式的实现过程中，通常需要多个子类必须实现某一个方法，在`crm`项目中，为了实现`自动分配客户资源`之后对销售股份发送`微信、短信、邮件信息`，就需要在不同的类中都实现`send`方法


## 实现的方式
#### 使用`raise NotImplementedError`实现
- 这种方式设计的非常巧妙，在`基类`中定义一个`派生类`必须要实现的方法，在这个方法内部直接`raise NotImplementedError(错误信息)`。当继承自派生类的子类调用了该方法时，会现在自己的类中找该方法，如果在自己类中找到了该方法，就会执行，如果没有找到就会去执行父类中的该方法，接着就会抛出`NotImplementedError`

###### 应用
1. 在Flask源码中，其内部的`Role`类就继承自`RoleFactory`类，并且`Role`中必须实现`get_rules(self, map)`方法，返回一个存放了匹配规则的可迭代对象。源码如下

```python
class Rule(RuleFactory):
     # 省略无关内容
    def get_rules(self, map):
         yield self

class RuleFactory(object):

    """As soon as you have more complex URL setups it's a good idea to use rule
    factories to avoid repetitive tasks.  Some of them are builtin, others can
    be added by subclassing `RuleFactory` and overriding `get_rules`.
    """

    def get_rules(self, map):
        """Subclasses of `RuleFactory` have to override this method and return
        an iterable of rules."""
        raise NotImplementedError()
```

2. Flask中除了`RoleFactory`实现了这个方法，每一次请求到来，为`RequestContext`对象绑定`session`时，用到的`SessionInterface`就利用了`raise NotImplementedError`的方式。所有继承了`SessionInterface`的派生类，比如`SecureCookieSessionInterface`内部就必须实现`open_session`和`save_session`方法

```python
class SessionInterface(object):
    def open_session(self, app, request):
        """This method has to be implemented and must either return ``None``
        in case the loading failed because of a configuration error or an
        instance of a session object which implements a dictionary like
        interface + the methods and attributes on :class:`SessionMixin`.
        """
        raise NotImplementedError()

    def save_session(self, app, session, response):
        """This is called for actual sessions returned by :meth:`open_session`
        at the end of the request.  This is still called during a request
        context so if you absolutely need access to the request you can do
        that.
        """
        raise NotImplementedError()
```

###### 项目应用
- 开发`crm`项目中，为了实现xxx
- mark待补充




#### 使用抽象类实现
- 使用抽象类实现需要用到`abc`模块，并在`基类`中实现`抽象方法`
- 缺点
    - mark待补充

```python
from abc import ABCMeta, abstractmethod


class RoleFactory(metaclass=ABCMeta):

    @abstractmethod
    def get_rules(self, map):
        pass


class Role(RoleFactory):
    def get_rules(self, map):
        yield self
```
