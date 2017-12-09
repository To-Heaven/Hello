# 获取文件本地路径

#### 应用
- 本地文件预加载显示（比如图片）

## 方法
#### 屌丝版
- 使用FileReader对象读取文件路径

```javascript
$("input:file").change(function(){
	fileObj = this.files[0];
	
	var reader = new FileReader();
	reader.readAsDataURL(fileObj);
	
	reader.onload = function(){
		$("#img_avatar")[0].src = reader.result;
	}
});

```


#### 简单粗暴版
- 直接使用window.url对象的createObjectURL方法

```javascript
$("input:file").change(function(){
	$("#img_avatar").src = window.url.createObjectURL(this.files[0]);
})
```
