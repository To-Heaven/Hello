

## head部分常用标签
#### 自闭合标签
- meta标签
	- 提供页面元信息，即搜索引擎搜索得到的页面关键字和页面的描述信息
	- `name`属性（要配合content属性使用）
		- 属性值为`keywords`页面关键字
			- `<meta name="keywords" content="python  MySQL Django HTML ">`
		- 属性值为`description`
			- `<meta name="description" content="这是一个分享知识和生活的网站">`
	- `http-equiv`属性（要配合content属性使用）  
		- 相当于HTTP文件头，向浏览器提供信息，从而正确显示页面内容，与其对应的是content属性，用于存放各个变量对应的变量值
		- 属性值为`Refresh` 自动刷新并指向新页面，content变量值格式 `倒计时秒;URL=https://www.example.com`
			- `<meta http-equiv="Refresh" content="10; URL=https://www.ziawang.com">`
		- 属性值为`content-Type`，提供内容类型
			- `<meta http-equiv="content-Type" charset="UTF8">` 
- title标签
	- 定义页面标题，显示在浏览器页面标签中

- link标签
	- 定义文档与外部资源的关系
	- `rel`属性
		- 属性值为`icon`定义页面标题左边的logo
			- `<link rel="icon" href="http://www.jd.com/favicon.ico">`
		- 属性值为`stylesheet`链接一个样式表
			- `<link rel="stylesheet" href="css.css">`
- script标签 

#### 非自闭合标签
- 待补充
