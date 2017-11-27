## localStorage、sessionStorage与document.referer

#### 背景
- 在进行Web开发时，经常遇到“需要在几个页面上使用同一个数据对象”的情况，比如客户端用户要对一个文章点赞的时候，如果用户没有登陆或者页面上没有保存用户相关的session，那么就使用`window.location.href`属性让用户跳转到登陆页面，并且我们还要保存用户要点赞的这个页面的URL，保证在用户登录之后会跳转到登陆之前的页面而不是home页面，这个时候，我们就需要一个变量，他可以让我们在浏览器上保存一个变量，并且这个变量在多个页面(多个js文件上都可以访问)，这个变量就是locationStorage

- 这些变量有点类似cookie

#### sessionStorage
- 会话存储只能存储浏览器的一次会话信息，通常当浏览器窗口关闭的时候，该对象内保存的数据将会被清空。

#### locationStorage
- 本地存储，故名思意，这个对象可以将数据存储在本地，当我们下次访问同一个网站的时候，网页就可以直接读取其中存放的信息。

#### 两者的使用方法
- 除了保存时间和保存的位置不同之外，两者的操作是相同的，用的最多的就是数据的存取操作

## 方法
#### setItem(str_key, str_value)
- 存储数据

#### getItem(str_key, str_value)
- 读取存储的数据

#### removeItem(str_key)
- 删除数据

#### clear()
- 清空对象保存的所有数据

## storage事件
- storage事件会在sessionStorage和locaStorage中的数据出现变化的时候触发
- 我们可以使用`window.addEventListener("event", callback_func)`来定义存储事件。
- clasback函数的参数是event事件对象，这个对象有三个属性
	- oldValue: 更新前的值，如果这个键被创建，则为null
	- newValue: 更新后的值，如果这个键被删除，则为null
	- url: 触发storage事件的url地址  
- **该事件不在导致数据变化的当前页面触发。如果浏览器同时打开一个域名下面的多个页面，当其中的一个页面改变sessionStorage或localStorage的数据时，其他所有页面的storage事件会被触发，而原始页面并不触发storage事件。可以通过这种机制，实现多个窗口之间的通信。所有浏览器之中，只有IE浏览器除外，它会在所有页面触发storage事件。**

