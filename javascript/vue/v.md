# 指令


#### v-model指令
作用
- 实现数据（data中的变量值）和视图（dom）的双向绑定

详细解释
- 所谓双向绑定，意思是将vue对象中的变量值与dom树中的元素value(input)属性绑定在一起，只要其中一个对象的值改变，另一个对象的值也会改变。
	1. 把元素的值和数据相绑定
	2. 当输入内容时，数据同步发生变化，实现视图到数据的驱动
	3. 当改变数据的时候，输入内容也会改变，实现了数据到视图的驱动

注意
- 当我们在`input`框中使用`v-model`并指定的`变量`的时候，`input`框中原先的`value`属性值就会被忽略，因此取input值的时候直接取变量对应的value就可以了

举例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="vue.js"></script>
</head>
<body>
    <div id="app">
        <input type="text" v-model="name" placeholder="姓名">
        <input type="text" v-model="age" placeholder="年龄">
        <input type="button" v-on:click="add()" value="添加">

        <table cellpadding="0" border="1">
            <tr v-for="(user, index) in users">
                <td>{{ user.name }}</td>
                <td>{{ user.age }}</td>
                <td>
                    <input type="button" value="删除" v-on:click="del(index)">
                </td>
            </tr>
        </table>
    </div>
<script>
    var vm = new Vue({
        el: "#app",
        data: {
            name: "",
            age: "",
            users: []
        },
        methods: {
            add: function () {
                vm.users.push({
                    name: vm.name,
                    age: vm.age
                })
            },
            del: function (index) {
                vm.users.splice(index, 1);
            }
        }
    })
</script>

</body>
</html>
```


#### v-text
作用
- 用于给标签赋予文本

注意
- `v-text`对应的`vue的变量`的值会覆盖调标签原有的文本值

举例

```html

<div id="app">
    <p v-text="p1">这里的值会被覆盖</p>
    <p v-text="p2"></p>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            p1: "文本1111111",
            p2: "文本2222222"
        }
    })
</script>
```


#### v-html
作用
- 既可以为标签赋予文本，又可以为标签插入html标签

注意
- 和`v-text`一样，同样会覆盖子标签和标签内的已有文本

```html
<div id="app">
    <div v-html="d1">
        <span>这个标签会被覆盖</span>
    </div>
    <div v-html="d2"></div>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            d1: '<input type="text">',
            d2: '<input type="text">'
        }
    })
</script>
```


#### v-if
作用
- 根据表达式返回的布尔值来动态的插入和删除元素。

注意
- 当`v-if`的值为`false`的时候，vue就会帮我们将该标签注释掉，实际上就是从dom树中删除了这个标签


- 举例

```html
<div id="app">
    <p v-if="p1">1</p>
    <p v-if="p2">2</p>
    <p v-if="p3">3</p>
    <p v-if="p4">4</p>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            p1: 1 > 2,
            p2: true,
            p3: false,
            p4: function () {
                return 1 - 2
            }
        }
    })
```


**与 `v-else`连用**

```html
<div id="app">
    <p v-if="p1">1</p>
    <p v-else>一</p>
    <p v-if="p2">2</p>
    <p v-else>二</p>
    <p v-if="p3">3</p>
    <p v-else>三</p>
    <p v-if="p4">4</p>
    <p v-else>四</p>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            p1: 1 > 2,
            p2: true,
            p3: false,
            p4: function () {
                return 1 - 2
            }
        }
    })
</script>
```

#### v-show
作用
- 通过表达式的值判断是否**显示**元素。

注意
- 它与`v-if`不同，`v-if`得到`false`的时候，会将标签从DOM树中移除。而`v-show`为`false`
的时候，会**为标签添加`display: none`属性，并不会移除标签**


举例 
- 模拟一下网易邮箱登陆的切换效果

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="vue.js"></script>
    <style>
        ul li {
            display: inline-block;
            list-style: none;
            border: 1px solid blue;
            width: 80px;
            height: 30px;
            line-height: 30px;
        }

        .code {
            display: inline-block;
            width: 80px;
            height: 80px;
            background-color: red;
        }
    </style>
</head>
<body>

<div id="app">
    <ul>
        <li v-on:click="change_mode(true)">二维码登陆</li>
        <li v-on:click="change_mode(false)">邮箱登陆</li>
    </ul>
    
    <div class="code" v-show="temp">
        假装这是二维码
    </div>
    <div id="email" v-show="!temp">
        <p><input type="text"></p>
        <p><input type="email"></p>
        <p><input type="button" value="提交"></p>
    </div>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            temp: true
        },
        methods:{
            change_mode: function (new_temp) {
                vm.temp = new_temp;
            }
        }
    })
</script>
```


#### v-for
作用
- 根据变量的值来渲染元素。
	- 如果变量是一个数组，那么就会遍历数组中的值
		- `v-for="value in arr"`只提供一个变量接收遍历的结果时，返回的是数组中的value
		- `v-for="(value, key) in arr"`提供两个变量接收遍历的结果时，返回的是数组中的value以及value在该数组中的索引index
	- 如果变量是一个数字，那么就相当与python中的`for i in range(变量)`
	- 如果变量是一个字符串，由于在JavaScript中，字符串本质上也是一个数组，因此得到的是字符串中的一个个字符。
	- 如果变量是一个对象，
		- 如果只提供了一个变量接收结果，就会返回对象的属性值
		- 如果提供了两个变量接收结果，就会返回对象的属性值和属性

举例(包含嵌套循环)

```html
<div id="app">
    <ul>
        <li v-for="(value, index) in str" v-bind:id="index">{{ value }}</li>
    </ul>

    <ul>
        <li v-for="value in num">{{ value }}</li>
    </ul>

    <ul>
        <li v-for="(value, index) in arr" v-bind:id="index">{{ value }}</li>
    </ul>

    <ul>
        <li v-for="(value, attr) in obj">{{ attr }}: {{ value }}</li>
    </ul>

    <ul>
        <li v-for="(obj, index) in objs">
            {{ obj.name }}, {{ obj.sex }}
        </li>
    </ul>

    <ul>
        <li v-for="(obj, index) in objs">
            <span v-for="(value, attr) in obj">{{ attr }}: {{ value }}</span>
        </li>
    </ul>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            str: 'hello',
            num: 5,
            arr: [1, 2, 3],
            obj: {
                name: 'ziawang',
                age: 25
            },
            objs: [
                {name: "ziawang", sex: "male"},
                {name: "wangzihao", sex: "male"}
            ]
        }
    })
</script>
```


#### v-on
作用
- 用来监听事件

格式
- `<input id="p" type="text" v-on:keyup="func()" name="x">`

简写方式
- `<input id="p" type="text" @keyup="func()" name="x">`


```html
<body>

<div id="app">
    <input id="p" type="text" v-on:keyup="func()" name="x">
    <p>{{ msg }}</p>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            msg: ""
        },
        methods: {  
            func: function () {
                var ele_input = document.getElementById('p');
                vm.msg = ele_input.value
            }
        }
    })
</script>
```

#### v-bind
作用
- 为标签绑定属性

格式
- `<input type="text" v-bind:id="var">`
- 也可以直接使用`冒号`，`<input type="text" :id="var">`

简写方式

- `<input type="text" :id="var">`

绑定class属性
- 绑定`class`属性的方式有两种
	- 方式1：使用对象的方式，但是**key必须是类名**，value为一个布尔值
		- `v-bind:class="{bk_2:temp2, bk_3:temp3}"`
	- 方式2：使用数组的方式，数组中存放的是vue变量，变量指向类名
		- `v-bind:class="[bk2, bk3]`


```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="vue.js"></script>
    <style>
        .bk_1 {
            background-color: blue;
            width: 300px;
            height: 200px;
        }

        .bk_2 {
            background-color: red;
            width: 300px;
            height: 200px;
        }

        .bk_3{
            border: 2px solid black;
        }

    </style>
</head>
<body>

<div id="app">
    <a href="http://www.qq.com" v-bind:title="msg">腾讯</a>
    <div v-bind:class="bk1"></div>
    <div v-bind:class="bk2"></div>

    <div v-bind:class="{bk_2:temp}">对象内可以放多组键值对，key是类名。而不是变量名</div>
    <div v-bind:class="[bk2, bk3]">列表中放的是以一个个变量</div>
</div>

<script>
    var vm = new Vue({       
        el: "#app",
        data: {
            msg: "where did you go?",
            bk1: "bk_1",
            bk2: "bk_2",
            bk3: "bk_3",
            temp: true
        }
    })
</script>
```

#### v-once
- 模板语法中的`Mustache 标签`将会被替代为vue对象属性的值。一旦vue对象上` msg `属性发生了改变，插值处的内容都会更新。如果你只想**一次性的插入值**，当`vue`对象的`msg`属性再次发生改变的时候，不再更新该插入的值，可以对标签使用`v-once`指令

```html
<span v-once>这个值是一次性修改的: {{ msg }}</span>

```