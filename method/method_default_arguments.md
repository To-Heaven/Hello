# 利用默认参数让函数/方法实现多个功能

## 概述
- 当在一个函数或方法中我们需要要实现不同的功能时（如果这个函数不需要根据功能再拆分成两个函数的前提下），每一个功能需要调用的参数有时是不相同的，甚至有时即使需要用到相同的参数，每一个功能所需要的值也是不相同的。
- 默认参数的好处在于
	1. 让程序具有可选择性。这个可选择性是我自己定义的，我觉得，一个函数要想在一段程序中扮演不同的功能，就需要为这个函数提供实现不同功能的结构来选择，默认参数可以实现这个。一般这个默认参数都设置为`None`，当需要实现某个功能时，再覆盖该默认参数即可

## 举例
#### 举一个我在自己的开源项目`seconds`中组装数据时的例子

- 先放相关代码
	- 下面返回的data列表中，有函数也有字符串。函数内部实现了两个功能，使用了一个`is_header`默认参数来在程序中选择使用不同的功能
		1. 生成表头数据
		2. 生成单元格数据

```python
    # 页面显示(权限相关)部分
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
```

- 再看看函数内部逻辑

```python
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


- 下面高潮到了

```python
from django.template import Library
from django.utils.safestring import mark_safe

register = Library()


@register.inclusion_tag(filename='curd/includes/show_table.html')
def list_table(config_obj, objects):
    """ 渲染并生成列表页面标签
    Args:
        config_obj: CURBConfig对象
        objects: QuerySet对象
    Return:
        返回一个上下文对象，用来渲染指定模板
    """

	# 生成一个存放表格数据的装饰器
    def generator_tr(objects):
        def generator_td(object):
            if config_obj.get_list_display():
                for field in config_obj.get_list_display():
                    if isinstance(field, str):
                        val = getattr(object, field)
                    else:
                        val = field(config_obj, object, is_header=False)
                    yield val
            else:
                yield from config_obj.model_class.objects.all()
        yield from [generator_td(object) for object in objects]
	
	# 生成表格的表头数据
    if config_obj.get_list_display():
        head_list = []
        for field in config_obj.get_list_display():
            if isinstance(field, str):
                verbose_name = config_obj.model_class._meta.get_field(field).verbose_name
            else:
                verbose_name = field(config_obj, is_header=True)
            head_list.append(verbose_name)
    else:
        head_list = [config_obj.model_class._meta.verbose_name_plural]

    return {"data": generator_tr(objects), "head_list": head_list}
```
