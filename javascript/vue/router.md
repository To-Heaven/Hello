# Vue.$router使用


## 生成URL
#### 用于页面跳转
- `this.$router.push({path: "/index", params:{}, query:{}})`表示跳转到`/index`页面

- 不需要嵌入额外的查询字符串的条件下也可以直接使用`this.$router.push('/index')`

#### 反向生成URL
条件
1. 在`src/index.js`中有该URL的路由匹配关系
2. 在该路由匹配中，设置了别名


使用（假设路由系统是下面这样）

```javascript
export default new Router({
    {
      path: '/login',
      name: 'login',
      component: Login
    },
});
```

1. 如果只反向生成该URL
	- `<router-link v-bind:to="{name: 'login'}"></router-link>`

2. 如果向为URL添加查询参数(querystring)
	- `<router-link v-bind:to="{name: 'login', query: {key: value}}"></router-link>`

3. 如果URL对应的是一个命名路由，即URL中包含类似Django命名正则匹配，就像下面这样。
	- 那么反向生成时，应该这样`<router-link v-bind:to="{name: 'course-detail', params: {id: 123}}"></router-link>`

```javascript
    {
      path: '/course/detail/:id/',
      name: 'course-detail',
      component: CourseDetailPage1
    },
```

## 获取URL信息
#### 获取当前路由的路径
- `$router.path`
	- 总是解析为绝对路径


#### 获取命名路由中的key对应的value
- `$router.params`
	- 一个`key/value`对象
	- 包含`动态片段和全匹配片段`，如果没有路由参数，就是一个空对象。



#### 获取路由中query string键值对信息
- `$route.query`
	- 一个 `key/value` 对象，表示 `URL` 查询参数
	- 对于路径 `/foo?user=1`，则有 `$route.query.user == 1`，如果没有查询参数，则是个空对象。

#### 获取路由名称
- `$router.name`

