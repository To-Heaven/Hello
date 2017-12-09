# 使用Ajax上传文件

## form中同步上传文件
#### form表单设置
- 要想上传文件，需要配置form表单的`enctype="multipart/form-data"`
- 如果我们没有在form表单中设置该参数，服务端就不能获取到上传的文件，此时返回的MultiValueDict是一个空的类字典对象`<MultiValueDict: {}>`


###### 关于enctype属性
- `enctype`**规定了form表单数据在发送到服务端之前要被编码的类型**
- 属性值有三种
	- 默认: `application/x-www-form-urlencoded`
		- 在发送到服务器之前，所有字符都会进行编码（空格转换为 "&" 符，特殊符号转换为 ASCII HEX 值）
	- `multipart/form-data`
		- 如果我们想使用form的input标签上传文件，就必须将属性设置为`multipart/form-data`，这种方式支持上传二进制文件（比如图片），但是对于form表单中的其他数据比如上传的用户名，到达服务端之后还是字符串(str)类型
	- `text/plain`
		- 这种编码只会将空格转换成"&"符，但是不会对特殊符号进行转码




#### 使用request.FILES获取文件对象
- 服务端通过`request.FILES`获取到的是一个`MultiValueDict`对象，结构如下
	- MultiValueDict{'filename': [文件对象1, 文件对象2, ...]}
	- 使用`get("name")`获取文件对象fileObj，这里的`"name"`是前端form表单中input对应的标签的name属性值，**如果没有咋子input中声明name，后端仍然无法得到文件内容**

###### get与getlist
- get方法会得到一个文件对象
- getlist方法类似于retuest.POST.getlist(field)，得到的是文件对象组成的列表
	



###### fileObj
- **文件内容为二进制**
- fileObj.name: 返回客户端上传文件的文件名。


```python
def getfile(request):
    if request.method == 'GET':
        return render(request, 'getfile.html')
    elif request.method == 'POST':
        name = request.POST.get('name')
        fileDict = request.FILES
        print(fileDict)
        print(name, type(name))
        fileObj = fileDict.get('readme')
        print(fileObj.name)
        return HttpResponse('上传成功')

# ---------------- 结果-----------------
<MultiValueDict: {'readme': [<InMemoryUploadedFile: review.txt (text/plain)>]}>
ziawang <class 'str'>
review.txt
```


## Ajax FormData


#### FormData
- 使用FormData不仅可以处理form表单中要提交的数据，还可以实现异步上传一个二进制文件

###### 1. 创建FormData对象
- `var formData = new FormData()`

###### 2. 为对象添加数据（键值对）
- `formData.append(key, value)`
	- key: 作为请求体内容的键，不一定要和form表单中标签name属性相同
	- value: 标签的value

```javascript
var upload_file=$("#img")[0].files[0];

$.ajax({
            url:"/upload_file/",
            type:"POST",
            data:formData,
            contentType:false,
            processData:false,})

```



- 使用jQuery获取文件的步骤（易错）
	1. `$("#img")`: 得到的是一个存放了input标签的集合
	2. `$("#img")[0]`: 去除input标签
	3. `$("#img")[0].files`: 得到一个存放文件对象的集合
	4. `$("#img")[0].files[0]`: 得到目标文件，我们要传递给formdata的对象就是它

```html
    <p>头像<input id="img" name="xxx" type="file"></p>
```



###### 3. 添加到Ajax参数中
- 我们将填充了数据的formdata对象作为参数值传递给data就可以了，但是我们必须还要在ajax的参数中添加一下两个参数才能使formdata生效
	- `contentType: false`
	- `processData: false`