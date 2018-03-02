# 使用vuex保存要共享变量


### 配置
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


### 使用
#### 调用state中的数据
- 使用`this.$store.state.username`调用state中存放的`username`



#### 调用mutations中的方法
- 使用`this.$store.commit("方法名", 方法参数)`调用
	- 方法的参数默认第一个传入的时`state`中的数据，我们在`commit`中传入的参数会作为后续位置参数传递给`commit中指定的方法`


#### 举例

```javascript
<script>
  export default {
    name: 'HelloWorld',
    data() {
      return {
        username:'',
        password:'',
        error:'',
      }
    },
    methods:{
      SubmitLogin: function (){
        let that = this;
        this.error = "";
        let name = this.username;
        let pwd = this.password;
        this.$axios.request({
          //url: this.$store.state.apiList.auth,
          url: 'http://127.0.0.1:8000/login/',
          method: 'POST',
          data: {
            username: this.username,
            password: this.password
          },
          responseType: 'json'
        }).then(function (response) {
          that.$store.commit('saveToken', response.data);
          let backUrl = that.$route.query.backurl;
          if (backUrl) {
            that.$router.push({path: backUrl})
          } else {
            that.$router.push('/index')
          }

        }).catch(function (error) {
          console.log(error)
        })
      }
    }
  }
</script>
```
