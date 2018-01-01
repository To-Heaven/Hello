# 封装数据的几种方式

#### 概述
- 在之前的总结文章中，我介绍了如何在项目中使用字符串来封装多个用于后端form验证的数据，除了字符串之外还有以下几种封装数据的方法
	1. `列表` 或 `集合`
	2. `字典` 或 `OrderDict有序字典`
	3. `类`

- 其实，在python中，**上述存储数据的结构比如`列表、集合、字典等`本质上都是用类存储的**，不信你可以翻源码看，python中一切皆对象在这里就是很好的体现

###### 1. 使用列表或集合封装
- 首先必须明确的是，**列表是有序的，集合是无序的**，集合封装数据的最大用途在我看来就是可以`去重`，所以如果你想存储有序或者有可能需要重复的数据时，就需要使用列表，而不是集合


###### 2. 使用字典或有序字典封装
- 你也要明确的是
	1. 字典是无序的，字典的`key`必须是不可变的（或者说可hash的），而有序字典是有序的
	2. 字典用键值对存放一组组数据

- 如果你想封装的数据既需要是一组组的键值对，又需要是有序的，有序字典可能更适合你。
	- ps：如果你是新手，不知道什么是有序字典，建议你翻一下python官方提供的标准库，里面有很多功能强大哦且pythonic的库，`collections`就是其中非常棒的一个库


###### 3. 使用类来封装数据
- 类封装数据的方式有很多种，可以封装到`类的实例对象`中，也可以封装到`类的静态字段`中。使用类封装数据与上面几种方式的最大的不同就是，你可以对封装的数据进行自定义功能，即具有较强的自由性。虽说上述几种方式封装数据的方式本质上也是使用的类，而且这些类提供了处理其本身数据结构的各种方法和属性，比如列表的`count、index`，字典的`items、values`等，但是如果要想实现自己的`业务逻辑`，你需要自定义一个类来封装功能。

#### 项目应用
###### seconds —— 使用类封装数据
- 在我的开源组件`seconds`中，组合搜索功能是可配置的，也就是说，在列表页面，用户可以自定义要被用来做组合搜素的字段，比如用户表中，你可以选择让`所属部门、性别`作为要用来进行组合搜索的字段，他们将分别占有一行，用来列出搜索选项，比如性别对应`男、女`。

- 为指定权限的用户配置组合搜索时，只需要在配置类中覆盖`CURDConfig`基类`cimbain_search_filed_list`即可，在该列表中，需要传递进入的就是一个个封装的每一个字段配置的`SearchOption`对象。该对象内封装了属性和功能
	- 属性：`是否是多选`、`是否是choices字段`、`字段名`等
	- 方法：
		1. 获取ForeignKey字段对应的多有记录对象，比如性别字段对应数据库中的`男`和`女`
		2. 获取包含choices的字段对应的多条记录对象

###### 代码实现

```python
# 1. SearchOption类
class SearchOption(object):
    """ 用于封装配置信息

    """

    def __init__(self, field_name, is_multi=False, condition=None, is_choices=False, text_func_name=None, val_func_name=None):
        self.field_name = field_name
        self.is_multi = is_multi
        self.condition = condition
        self.is_choices = is_choices
        self.text_func_name = text_func_name
        self.val_func_name = val_func_name

    def get_choices(self, field):
        """ 获取字段的choices参数对应列表
        Args:
            field: 一个初始化了choices参数的field对象
        Return:
            返回choices二元元组组成的列表
        """

        return field.choices

    def get_queryset(self, field):
        """ 获取ForeignKey/ManyToManyKey关联的主键表的满足筛选条件的所有记录对象
        Args:
            field: 一个ForeignKey或者ManyToManyKey字段对象
        Return:
            返回主键表满足条件的记录对象组成的QuerySet对象
        """

        if self.condition:
            try:
                results = field.related_model.objects.filter(**self.condition)      # Dajngo2.0, 也可使用"field.model"
            except AttributeError as e:
                print(e)
                results = field.rel.to.objects.filter(**self.condition)             # Django2.0以下版本
            return results
        return field.related_model.objects.all()

# 2. 配置组合搜索
class UserConfig(CURDConfig):
    """ 用户表配置
    
    """
    
    list_display = ['name', 'login_name', 'login_password', 'email', 'department']
    combain_search_field_list = [
        SearchOption(
            field_name='department',
            text_func_name=lambda x: str(x),
            val_func_name=lambda x: x.numbering  # 自定义的主键
        )
    ]
    edit_link = ['name']
    show_search_form = True
    search_list = ['name__contains', 'login_name__contains']
```



