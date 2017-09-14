# 上下文管理

## example of pyMySQL

```python
# 简单的执行命令
import pymysql

connection = pymysql.connect(host='localhost',
                             user='root',
                             password='pass',
                             database='db0914',
                             cursorclass=pymysql.cursors.DictCursor,
                             charset='utf8')

try:
    with connection.cursor() as cursor:
        sql = 'insert into users(email, password) VALUES (%s, %s)'
        cursor.execute(sql, ('ziawang_python@163.com', 'pass'))
        connection.commit()

    with connection.cursor() as cursor:
        sql = 'select email, password from users where id=%s'
        cursor.execute(sql, (1,))
        result = cursor.fetchone()
        print(result)
finally:
    connection.close()

```

```
# 模拟验证登陆
import pymysql

connection = pymysql.connect(host='localhost',
                             user='root',
                             password='pass',
                             database='db0914',
                             charset='utf8',
                             cursorclass=pymysql.cursors.DictCursor)

try:
    with connection.cursor() as cursor:
        while True:
            email = input('>>>').strip()
            password = input('>>>').strip()
            sql = 'select * from users where email=%s and password=%s'
            res = cursor.execute(sql, (email, password))
            if res:
                print('login successful')
                break
            else:
                print('login failed, input "quit" to quit login')
finally:
    connection.close()
```
