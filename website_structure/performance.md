# Web应用的性能
https://www.cnblogs.com/sunshq/p/4478461.html	
http://blog.jobbole.com/94962/

## 服务端配置方面
#### 1. 使用反向代理nginx


## Web前端显示(加载性能)
#### 1. 减少额外请求次数
- 在网页加载过程中，对于页面中类似图片标签需要客户端再次向服务端发送请求的资源，应尽可能的将这些资源整合在一起，比如将网页中可能用到的多个小图标做成一个长图片，这样客户端只需要向服务端发送一次请求就可以将需要被渲染的图标数据一次性获取到。
#### 2. 预先读取资源
###### 2.1 预先读取整个页面的内容
- 使用`link`标签的`rel`属性可以在当前页面加载时，预先从服务端获取指定路径对应的页面，**注意预先加载的页面应该是用户访问率较高的页面**
- 比如现在在`www.ziawang.com/index`下，用户访问这个网站之后最常访问的是`www.ziawang.com/python`，那我们可以这样写 

```html
<link rel="prerender" href="/python/">
```

###### 2.2 预先读取指定资源
- 可以为我们的页面预先加载css、img、js等资源

```html
<link rel="prefetch" href="https://cdn.bootcss.com/font-awesome/4.7.0/css/font-awesome.min.css
">
```

###### 2.3 预先解析dns
- 当页面上存在其他域的超链接时，可以预先解析该域的dns
	- 比如现在在`www.ziawang.com/index`下有一个指向github的超链接，我们为了提高客户端的浏览速度体验，可以让浏览器预解析github的域

```html
<link ref="dns-prefeatch" href=""https://github.com/ZiaWang>
```


#### 3. 控制资源的读取顺序——懒加载
- 将页面中不需要被立即读取的资源比如图片或css文件等

###### lazyload属性和postpone属性
- 指定了lazyload属性的资源标签，会延迟于其他资源之后再读取
- 而指定了postpone属性的资源标签，会在页面完全显示之后再读取

###### 两者的使用方式
- 直接在标签中指定
	- `<link ref="stylesheet" href="/static/test.css"> lazyload`
	- `<img src="media/avatar/ziawang.png" postpone>`
	
- 在css中指定
	
```css
img#ziawang{
		resource-priorities: lazy-load;
}

img#haha{
		resource-priorities: postpone;	
}

```


- 当然你也可以在js中为标签的`setAttribute`方法设置属性 


#### 使用客户端缓存


## 后端数据
#### 使用yield处理二次数据

