# 使用type动态创建类

## 介绍和使用
- python中一切皆对象，就算是类他也是一个对象，包括元类。

#### type动态创建类
- 格式
	- 第一个参数是类的名称，定义要符合类名称的定义规范。
	- 第二个参数是该类将要继承的父类的元组
	- 第三个参数是一个封装了类的字段的字典
		- 字典的key就是字段名（本质上就是一个变量名），value对应该字段名变量指向的对象。
		- 字段名可以是一个类名（比如Django ModelForm中的Meta），也可以是一个个由`字段名变量: 字段对象`构成的键值对（比如Django Form类）

```python
var = type("str_classname", (par_class1, par_class2), fields_dict)

```

- 举例

```python
form_class = type(
                    'LoadScoreModelForm',
                    (Form,),
                    {
                        'score_%s' % study_record.id : fields.ChoiceField(choices=models.StudyRecord.score_choices,
                                                                          widget=widgets.TextInput(attrs={"class": "form-control"})),
                        'homework_note_%s' % study_record.id: fields.CharField(max_length=520,
                                                          widget= widgets.Textarea(attrs={'placeholder': '作业评语',
                                                                                         "class": "form-control",
                                                                                          "cols": 30, "rows": 2}))
                    }
                )
```


```python
            meta_class = type(
                'Meta',
                (object, ),
                {"model": self.model_class,
                 "fields": "__all__"}
            )

            ViewModelForm = type(
                'ViewModelForm',
                (ModelForm, ),
                {"Meta": meta_class}
            )

```


#### type动态创建类的特点
-  可以动态灵活的创建类，比如在调查问卷项目中，创建了一个动态的form组件
	-  对于`type`创建类的第三个参数，我们可以循环动态的生成字段再传递到字典中，如果你使用的是`class`创建类，就没法如此动态灵活


## 项目应用
- 在本人接触的大小项目中，几乎每一个涉及到表单操作的项目，都有用到了`form`组件，当需要动态的生成指定字段名的表单类时，一般选择`type`来动态生成`Form或者ModelForm表单类`

- 有关调查问卷的项目应用，你可以去我`总结`栏目下的`《Web 开发中，字符串使用的正确姿势》下查看`