# Vue 项目搭建和配置



## 搭建项目
#### 修改Vue源
- `npm config set registry https://registry.npm.taobao.org  `


#### 搭建脚手架
- `npm init webpack vue项目名称`



#### 启动
1. 进入到上述"vue项目名称目录下"
2. `npm run dev`
	- 注意是`dev`，不是你的项目名称


## 项目主要文件介绍
#### src/router/index.js
- 路由配置文件


#### src/components/
- 存放组件的目录
- **组件将在`src/router/index.js`中导入使用**
	- 分配匹配规则
	- 创建别名


## 管理插件

#### 常用的方式
1. 添加全局方法或属性
2. 添加全局资源
3. 通过mixin方法添加一些组件选项
4. 添加Vue实例方法，这个通过`Vue.prototype`实现

#### 使用`Vue.prorotype`添加实例方法
- `Vue.prototype.$axios = axios`

#### 通过`Vue.use()`使用插件
- `Vue.use(Vuex)`
- 注意	
	- `Vue.use()`会自动阻止多次注册相同插件，即只会注册一次该插件


## 其它扩展组件
#### 注意
1. 安装组件的时候，一定要在Vue项目的目录下执行`npm install xxx`命令，因为执行此命令的时候需要使用到Vue项目的`package.json`文件，如果没在执行命令的目录下找到该文件，就会抛出错误

2. 成功安装之后，就会在Vue项目的`node_modules`目录下生成组件对应的目录，比如`node_modules/axios/`

#### axios
- 这个组件用于我们异步提交请求，其内部实际上就是封装了XMLHttpRequest，类似与jQuery的`$.ajax`

安装和配置
1. 安装：`npm install axios`
2. 导入使用
	1. 在`src/main.js`下导入`axios`
	2. 在其它文件中使用`Vue.axios`调用

使用
- []()

```javascript
// src/main.js
import axios from "axios"

Vue.prototype.$axios = axios
```

#### vuex
- 这个组件用于保存各个组件佛能够用的共享变量

安装和配置
1. 安装vuex `npm install vuex`
2. 配置
	1. `src`下创建目录`store`
	2. `store`下创建`store.js`
		1. 该文件中用于导入和初始化`vuex`
	3. `src/main.js`文件中导入`store`并初始化

```javascript
// store.js文件
import Vue from 'vue'
import Vuex from 'vuex'
import Cookie from 'vue-cookies'

Vue.use(Vuex);


export default new Vuex.Store({
  // 组件中通过 this.$store.state.username 调用
  state: {
    username: Cookie.get('username'),
    token: Cookie.get('token'),
    apiList: {
      auth: 'http://api.luffy.com:8000/v1/auth/',
      courses: 'http://api.luffy.com:8000/v1/courses/',
      pricePolicy: 'http://api.luffy.com:8000/v1/price_policy/',
      shopCar: 'http://api.luffy.com:8000/v1/shop_car/',
    }
  },

  mutations: {
    // 组件中通过 this.$store.commit(参数)  调用
    saveToken: function (state, userToken) {
      state.username = userToken.username;
      Cookie.set("username", userToken.username, "20min");
      Cookie.set("token", userToken.token, "20min");

    },
    clearToken: function (state) {
      state.username = null;
      Cookie.remove('username');
      Cookie.remove('token');

    }
  }
})
```


```javascript
// main.js
import Vue from 'vue'
import App from './App'
import router from './router'
import store from './store/store'
import axios from "axios"

Vue.prototype.$axios = axios;


Vue.config.productionTip = false;

/* eslint-disable no-new */
new Vue({
  el: '#app',
  store,				// 实例化时作为参数传入
  router,			
  components: {App},
  template: '<App/>'
});
```




#### vue-cookies
为啥要使用这个？
- 每一次刷新页面的时候，Vue都会清空页面对应组件的缓存，全局变量也会被清空，所以对于一些必要的数据我们需要保存在cookie中

安装和配置
1. 安装`npm install vue-cookies` 
2. 使用`
	- 直接导入`Cookie`对象进行操作即可
	- `import Cookie from "vuw-cookies"`







## 注意事项
#### 组件
1. 每一个组件的`template`下只能有一个根元素，通常使用一个`div`来将所有其它元素放在其中，如果你直接将多个元素并列放在`<template></template>`标签内，就会报错
