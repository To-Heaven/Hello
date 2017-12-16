# 钩子函数扩展功能与实现默认功能

## 概述
- 在Web开发中，有这么一个场景，我们开发一个组件，这个组件的功能是可以被用户来扩展甚至是覆盖的，我们需要为其扩展和覆盖来提供一个接口，在开发中通常我会选择**使用钩子函数配合初始数据结构来实现**
	- **所谓的初始数据结构其实就是一个空的数据接口，这个数据结构是提供给组件使用者来操作的，我们要做的就是在代码中用另一个相同的数据结构来存放默认的功能，如果用户扩展了该功能，我们只需要将用户扩展的数据结构通过钩子函数获取并整合在一起即可**
	- **钩子函数是提供给使用者用来扩展功能的，一般用来扩展功能的钩子函数扩展的对象都是某种数据结构，并将该数据返回，然后在其他代码中调用该数据**

## 举例
###### 说了这么多，代码才是最实在的，下面放代码及相关解释

1. 默认只实现4个路由映射关系，用户通过钩子函数扩展路有关系

```   
class CURDConfig:
    """  为每一个`model_class`模型类生成url路径与视图函数之间的映射关系

    """

    list_display = []           # 存放列表页面表格要显示的字段

    def __init__(self, model_class, curb_site_obj):
        self.model_class = model_class
        self.site = curb_site_obj

	 def get_app_model(self):
        """ 获取当前模型类的名称和该模型类所在应用的名称
        Return:
              返回一个存放二元元组: (模型类名, 应用名)
        """

        app_model = (
            self.model_class._meta.app_label,
            self.model_class._meta.model_name
        )
        return app_model

    def get_urls(self):
        """ 生成路径与视图函数映射关系的列表，并扩展该列表
        Return:
            返回存放路由关系的列表
        """

        # 生成增、删、改、查基本映射关系
        app_model = self.get_app_model()
        urlpatterns = [
            re_path(r'^$', self.show_view, name='%s_%s_show' % app_model),
            re_path(r'^add/$', self.add_view, name='%s_%s_add' % app_model),
            re_path(r'^(\d+)/delete/$', self.delete_view, name='%s_%s_delete' % app_model),
            re_path(r'^(\d+)/change/$', self.change_view, name='%s_%s_change' % app_model),
        ]

        # 扩展路由映射关系
        extra_patterns = self.extra_url()
        urlpatterns.extend(extra_patterns)
        return urlpatterns

    @property
    def urls(self):
        return self.get_urls()

    def extra_url(self):
        """ 为用户扩展urls提供的接口，只需要在CURDConfig派生类中派生覆盖此方法即可
        Return:
            存放了路径与视图函数映射关系的列表(re_path, path, url)
        """

        return []
```


- 上述extra方法就是提供给使用者的一个扩展功能的钩子函数，使用者只需要返回一个存放路由映射关系的列表，并在派生类中实现对应的视图函数即可

2. get_list_display默认为一行数据提供删除、修改、选择框，使用者可以通过`list_display`来扩展其想要增加的其他功能
	- 比如，在派生类中可以这样，`list_display = ["name", "age", like]`，前面两个是要显示的字段，后面的函数名对应要增加的功能。

```python
	list_display = []           # 存放列表页面表格要显示的字段

    def get_list_display(self):
        """ 处理用户权限之内的相关操作，派生CURDConfig类中可以根据用户权限来分配响应的功能按钮/链接，
            实现每个类中可以在增删改查之外，再扩展自己类的URL
        Return:
            包含了权限操作按钮/链接在内的列表
        """

        data = []
        if self.list_display:
            # data.extend(self.list_display)    # 不能是self调用，与后面链接起来，got multiple values for argument 'is_header'
            data.extend(self.list_display)
            data.append(CURDConfig.delete)
            data.append(CURDConfig.change)
            data.insert(0, CURDConfig.checkbox)
        return data

    show_add_btn = False        # 先否显示添加按钮权限接口

    def get_show_add_btn(self):
        """ CURDConfig中，根据用户权限来判断用户是否有添加记录按钮对应的权限，如果有则返回True
        Return:
            布尔值，代表用户是否有权限，该值将传递给上下文中用于渲染页面添加按钮
        """

        # 中间存放业务逻辑
        return self.show_add_btn

    # 定制列表页面要显示的列（功能按钮/链接）
    def delete(self, obj=None, is_header=False):
        """ 列表页面单条记录删除按钮/链接
        Args:
            obj: 该记录对象
            is_header: 当用于生成列表标题"<th>"的时候为True
        Return:
            一个SafeText对象，可以将字符串中的html内容转化为标签，
            此处为删除功能的超链接
        """

        if is_header:
            return '删除'
        return mark_safe('<a href="%s">删除</a>' % (self.get_delete_url(obj.id), ))

    def change(self, obj=None, is_header=False):
        """ 列表页面单条记录编辑按钮/链接
        Args:
            obj: 该记录对象
            is_header: 当用于生成列表标题"<th>"的时候为True
        Return:
            编辑功能超链接
        """

        if is_header:
            return '编辑'
        return mark_safe('<a href="%s">编辑</a>' % (self.get_change_url(obj.id), ))

    def checkbox(self, obj=None, is_header=False):
        """ 列表页面单条记录的选择checkbox，用于批量记录操作
        Args:
            obj: 该checkbox所在记录对象
            is_header: 当用于生成列表标题"<th>"的时候为True
        Return:
            关于选择该条记录的checkbox框
        """

        if is_header:
            return '选择'
        return mark_safe('<input type="checkbox" name="id" value="%s" />' % (obj.id, ))
```