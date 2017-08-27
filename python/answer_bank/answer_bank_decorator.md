##  1. 编写函数，（函数执行的时间是随机的）
## 2. 编写装饰器，为函数加上统计时间的功能
```python

import time
import random

def timmer(func):
    def inner(*args, **kwargs):
        sleep_time = random.randrange(1, 5)
        start = time.time()
        time.sleep(sleep_time)
        res = func(*args, **kwargs)
        print('total time : %s'%(time.time()- start))
        return res
    return inner

@timmer
def f():
    print('f run..')

f()
```

##  3. 编写装饰器，为函数加上认证的功能
##  4. 编写装饰器，为多个函数加上认证的功能（用户的账号密码来源于文件），要求登录成功一次，后续的函数都无需再输入用户名和密码

```
# 文件提取信息使用eval()即可，这里直接将字典放在全局作用域中
user_status = {
    'auth':True,
    'name':'ziawang',
    'pwd':'biubiubiu'
}


def auth(func):
    def inner(*args, **kwargs):
        if user_status['auth']:
            while True:
                name = input('your name:').strip()
                pwd = input('your password:').strip()
                if name == user_status['name'] and pwd == user_status['pwd']:
                    print('登陆成功！')
                    user_status['auth'] = False
                    break
        res = func(*args, **kwargs)
        return res
    return inner

@auth
def f1():
    print('f1 run ')

@auth
def f2():
    print('f2 run ')

@auth
def f3():
    print('f3 run ')

f1()
f2()
f3()
```


## 5. 编写装饰器，为多个函数加上认证功能，要求登录成功一次，在超时时间内无需重复登录，超过了超时时间，则必须重新登录

```python
import time
from threading import Timer


def change_user_status():
    user_status['auth'] = True


user_status = {
    'auth':True,
    'name':'ziawang',
    'pwd':'biubiubiu'
}

# 设置时间为10秒
my_timer = Timer(10, change_user_status)



def auth(func):
    def inner(*args, **kwargs):
        if user_status['auth']:
            while True:
                name = input('your name:').strip()
                pwd = input('your password:').strip()
                if name == user_status['name'] and pwd == user_status['pwd']:
                    print('登陆成功！')
                    user_status['auth'] = False
                    break
        res = func(*args, **kwargs)
        return res
    return inner

@auth
def f1():
    print('f1 run ')

@auth
def f2():
    print('f2 run ')

@auth
def f3():
    print('f3 run ')

f1()
my_timer.start()
time.sleep(5)
f2()
time.sleep(6)
f3()
```






















