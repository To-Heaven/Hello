## 解决问题的思路——存储重要的值
- 使用变量存储上下文中需要使用到的值

#### 背景
- 函数式编程中，嵌套多层函数的多个函数的逻辑处理需要用到同一个对象的值时，将这个值声明为全局变量，或者将这个值存放在一个container中，这样对这个对象的修改就是全局的，我们就可以利用这个值来实现多个函数之间的"联系"

#### 延伸
- 这种思想应用的很广泛，在并发编程中更甚，它实现了多个进程之间的通信。
- 当然，对于线程来说，由于同一个进程下的多个线程公用一个进程的资源，因此有时候我们也想让线程之间的资源时相互隔离的，我们可以使用`ThreadLocal`来创建一个针对单个线程的全局变量，这个应用在Flask框架中也有，比如常用Flask常用的上下文对象request、g、session等 


#### 举例
###### 在JavaScript中的应用（以登陆验证为例）

```html
    <div class="col-md-6 col-md-offset-3" style="color: black; margin-top: 100px">
        <div class="row">
            <div class="col-md-4 col-md-offset-3">
                <h2>login</h2>
            </div>
        </div>
        <form class="form-horizontal" method="post" action="/login/">
            {% csrf_token %}
            <div class="form-group form-group-lg">
                <label for="username" class="col-sm-2 control-label">username</label>
                <div class="col-sm-5">
                    <input type="text" class="form-control" id="username" name="username"
                           aria-describedby="helpBlock2"  placeholder="username">
                    <span id="helpBlock2" class="help-block"></span>
                </div>

            </div>
            <div class="form-group form-group-lg">
                <label for="password" class="col-sm-2 control-label">password</label>
                <div class="col-sm-5">
                    <input type="password" class="form-control" id="password" name="password"
                           aria-describedby="helpBlock3" placeholder="password">
                    <span id="helpBlock3" class="help-block"></span>
                </div>
            </div>
            <div class="form-group">
                <div class="col-sm-offset-3  col-sm-6">
                    <button type="submit" class="btn btn-lg btn-success">登陆</button>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                    <a href="/register/">
                        <button type="button" class="btn btn-lg btn-primary">注册</button>
                    </a>
                </div>
            </div>
        </form>
    </div>
    <script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>
    <script src="https://cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
    <script>
        $('form[action="/login/"] button[type="submit"]').click(function () {

                s = true;						// 创建全局变量

                $('form[action="/login/"] input:gt(0)').each(function () {
                $(this).parent().parent().removeClass('has-error');
                if ($(this).val().length === 0){
                    $(this).parent().parent().addClass('has-error');
                    $(this).next().text(this.name + "can not be empty");
                    s = false;					// 修改全局变量
                    return s;				
                }
            });
            return s;							// 返回修改后的全局变量
        })
    </script>
```


###### 在python中的应用(以模拟缓存为例)

```python

```