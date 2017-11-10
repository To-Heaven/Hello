# 表设计


## 要注意的地方
1. 属性相同的要归类到同一张表中
2. 联表操作有性能消耗，是否要使用联表，在Django中，分以下两种情况
	1. 当主键表中记录个数经常变动的时候，使用联表操作
		- 比如
	2. 当主键表中记录个数固定不变或很少变动的时候，将主键表和外键表合并成一个表，在表中使用choice代替主键表中的记录。使用这种方式修改choice对应数据的时候需要重启服务端
		- 比如新闻类型与新闻之间


```python
# models.py 使用联表

class UserType(models.Model):
					"""
					用户类型表,个数经常变动
					"""
					title = models.CharField(max_length=32)

class UserInfo(models.Model):
	"""
	用户表：讲师和班主任
	"""
	username = models.CharField(max_length=32)
	password = models.CharField(max_length=64)
	email = models.CharField(max_length=32)
	ut = models.ForeignKey(to="UserType")
```


```python
# models.py 使用choice代替联表

class UserInfo(models.Model):
"""
用户表
"""
username = models.CharField(max_length=32)
password = models.CharField(max_length=64)
email = models.CharField(max_length=32)

user_type_choices = (
  (1, '班主任'),
  (2, '老师'),
)
user_type_id = models.IntegerField(choices=user_type_choices)
		
```

3. Django中设计表时，如果一张表中存在多个ForeignKey外键字段或ManyToMany字段的时候，方向查询会出错，应该自定义`related_name`参数代替默认的`xxx_set`


