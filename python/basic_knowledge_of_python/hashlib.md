## Hash_obj attributes
- hash.digest_size
	- The size of the resulting hash in bytes.


## Hash Method
- hash.update(arg)
	-  update the hash object with the object arg
		-  __arg must be a buffer of bytes.__
	- Note: repeated calls are equvalent to a single call with the concatenation of all the arguments
		- obj.update(a) + obj.update(b) = obj.update(a+b)

```python
import hashlib

obj_1 = hashlib.md5()
obj_2 = hashlib.md5()

a = 'hash'
b = 'lib'
c = a + b

obj_1.update(a.encode('utf-8'))
obj_1.update(b.encode('utf-8'))

obj_2.update(c.encode('utf-8'))
print(obj_1.hexdigest())
print(obj_2.hexdigest())

--------------------------------------------------------------------------------
# 运行结果

011b172c1b0410b994a7324a2406b262
011b172c1b0410b994a7324a2406b262
```

- **注意：**bytes类型可以使由Unicode类型字符串经过任意编码转换而来的，不同编码的到的bytes数据update得到的数据是相同的

```python

obj = hashlib.md5()
obj.update('ziawang'.encode('gbk'))

obj_clone = obj.copy()

other_obj = hashlib.md5()
other_obj.update('ziawang'.encode('utf-8'))

print(obj.hexdigest())
print(other_obj.hexdigest())
-------------------------------------------------------------------------------------
6d58ee4789cc6b68f6bf880ddebf0e9f
6d58ee4789cc6b68f6bf880ddebf0e9f
```
 
- 为了让代码看起来更加简洁，可以直接在字符串前面加上  ' b ' 来代替encode


```python
obj = hashlib.md5()
obj.update('ziawang'.encode('gbk'))

obj_clone = obj.copy()

other_obj = hashlib.md5()
other_obj.update(b'ziawang')

-----------------------------------------------------------------------------
# 运行结果
6d58ee4789cc6b68f6bf880ddebf0e9f
6d58ee4789cc6b68f6bf880ddebf0e9f

```

> digest		摘要  
> algorithm	算法  	
> interchangeable	可以互换的  
> interpreterable	可以判断的  
> concatentation	一系列互相关联的事物  

- hash.digest()
	- return the digest of the data passed to the update() method so far.
	- this is a bytes object of size digest_size which may contain bytes in the whole range from 0 to 255

- hash.hexdigest()
	- return a **string** of double length contain only hexadecimal digits. 
	- used to exchange the value safety in email or other non-binary environments

```python

import hashlib
#
obj_1 = hashlib.md5()
obj_2 = hashlib.md5()

a = 'hash'
b = 'lib'
c = a + b

# obj_1.update(a.encode('utf-8'))
# obj_1.update(b.encode('utf-8'))

obj_2.update(c.encode('utf-8'))
# print(obj_1.hexdigest())
print(obj_2.hexdigest())
print(obj_2.digest())

----------------------------------------------------------------------
# 运行结果
011b172c1b0410b994a7324a2406b262
b'\x01\x1b\x17,\x1b\x04\x10\xb9\x94\xa72J$\x06\xb2b'
```

> conpute 计算

- hash.copy()
	- return a copy or clone of the hash object
	- used to efficently compute the digests of data sharing a common initial substring

```python
obj = hashlib.md5()
obj.update('ziawang'.encode('utf-8'))

obj_clone = obj.copy()

print(obj)
print(obj_clone)
print(obj == obj_clone)					# 注意结果，得到的是个False
print(obj.hexdigest())
print(obj_clone.hexdigest())
----------------------------------------------------------------------------------
#运行结果
<md5 HASH object @ 0x000001DFFE12D3C8>
<md5 HASH object @ 0x000001DFFE12D468>
False
6d58ee4789cc6b68f6bf880ddebf0e9f
6d58ee4789cc6b68f6bf880ddebf0e9f
```

> collision	 碰撞
> signature       特征

## salt
- seting salt parameter users can introduce randomization to the hash function.
- useful for protecting against collision attacks on hash function 

```python
import hashlib

name = input('name:').strip()
pwd = input('password:').strip()

md5_obj = hashlib.md5()
md5_obj_salt = hashlib.md5(name.encode('utf-8'))

md5_obj.update(pwd.encode('utf-8'))
md5_obj_salt.update(pwd.encode('utf-8'))

print('md5_obj ==> ', md5_obj.hexdigest())
print('md5_obj_salt ==> ', md5_obj_salt.hexdigest())
------------------------------------------------------------------------------------------
name:ziawang
password:pass
md5_obj ==>  1a1dc91c907325c69271ddf0c944bc72
md5_obj_salt ==>  0098f381d5b7ad1fe4b03824fb2dcbee

Process finished with exit code 0

```





##  应用
- 文件校验

```python
# 普通文本文件
filesize = os.path.getsize('test.txt')
with open('test.txt', 'r') as t:
    obj = hashlib.md5('test.txt'.encode('utf-8'))
    for line in t:
        obj.update(line.encode('utf-8'))
    result = obj.hexdigest()
    # result = obj.digest()
print(result)

# 二进制文件
import os
import hashlib

obj = hashlib.md5()
with open('filename', 'r') as f:
    filesize = os.path.getsize('filename')
    while filesize>0:
        if filesize > 1024:
            content = f.read(1024)
        else:
            content = f.read(filesize)
        filesize -= 1024
        obj.update(content.encode('utf-8'))
print(obj.hexdigest())
```

- 登陆验证
	- 存放在文件中的用户名和密码应该是经过md5计算过的一串字符
	- 不能解密。但可以撞库，避免被撞库破解的方法是加盐


