# Dom节点对象常用属性

## 用于节点自身
#### 获取节点名
- `dom.tagName`

#### 设置类
##### 1. 使用dom.classList
- `dom.classList.add(str_cls)`
- `dom.classList.remove(str_cls)`
- `dom.classList`

##### 2. 使用dom.className
- `dom.className = "ziawang  haha xixi"`
	- 设置三个类"ziawang  haha xixi"

#### 设置id
- `dom.id = 'xx'`

#### 设置value
- `dom.value = 'xxx'`

#### img的常用属性
- `img_dom.height`
- `img_dom.width`
- `img_dom.src`
	- 获取或设置图片路径，当图片路径被设置后，会立马局部刷新该图片



#### button的常用属性
- `btn_dom.type = "submit"`
- `btn_dom.value = "提交"`
- `btn_dom.form`
	- 返回包含改按钮的表单dom对象的引用
- `btn_dom.disabled`
	- 设置或返回是否禁用该按钮

#### checkbox的常用属性
- `che_dom.form`
	- 返回包含改按钮的表单dom对象的引用

- `che_dom.disabled`
	- 设置或返回是否禁用checkbox

- `dom.checked`
	- 设置选中状态

#### Text与password
- 分别对应表单中input类型为`text`和`password`

- `dom.readOnly`
	- 设置文本框为只读类型
#### Radio
- `dom.checked`
	- 设置单选被选择的状态

#### option
- `dom.selected`
	- 设置被选中状态


#### FileUpload
- `dom.value`要上传的文件的文件名
- `dom.form`
	- 返回包含改按钮的表单dom对象的引用
- `dom.accept`
	- 设置或返回文件传输的MIME类型列表，用逗号分隔

## 用于获取其他节点
- ele.parElement
	- 返回节点的父节点对象
- ele.children
	- 返回一个子节点对象组成的列表
	- 节点的属性
	- 返回该节点下的子节点（不包含子节点的子节点。。。）
- ele.firstElementChild
	- 返回子节点列表中的第一个子节点
- ele.lastElementChild
	- 返回节点列表中最后一个子节点 
- nextElementSibling
	- 下一个兄弟标签元素
- previousElementSibiling
	- 上一个兄弟标签元素