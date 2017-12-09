# 撸代码必须遵守的规则

## 1. 变量名不要重复！！ 变量名不要重复！！ 变量名不要重复！！
- **特别是在项目中，众多文件共存的时候，相同的变量名引起的BUG在解决的时候是最耗时的**

#### 我的经典案例
- 在开发博客cms系统的时候，用户后台管理页面在添加文章和编辑文章的时候会使用到编辑器，选择的是kindeditor编辑器，使用这个编辑器需要在js文件中创建KindEditor对象，由于在addArticle.js和editArticle.js中都用到了编辑器，并且两者模板页面都继承自home页面，于是我将这两个文件都在home.html中导入了，最后出现了莫名其妙无法获取用户编辑的文章内容的BUG，原因就是两个页面的编辑器出现了冲突，在同一时刻，home页面中不能同时导入两个编辑器js代码！！即使变量名不同

- 解决
	- 强迫症的我不但把两个编辑器的名称改了，并利用模板的`{% block xxx %}`将两个js完全隔离，实现同一时刻下只能存在一个编辑器js代码
	- 如下

```html
<!-- home.html -->

{% block select_editor %}
	<script src="/static/js/addArticle.js"></script>
{% endblock %}
```


```html
<!-- editArticle.html -->

{% block select_editor %}
	<script src="/static/js/editArticle.js"></script>
{% endblock %}
```
