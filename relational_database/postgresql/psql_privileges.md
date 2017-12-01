# 权限

## 对象(数据库、表)的初始权限
- 当我们使用一个数据库用户比如ziawang创建一个数据库或者表的时候，备被创建的数据库或者表的所有者就是ziawang，一个数据库或者表在刚创建后的初始状态下，只有所有者才具备对其任意操作的权限，如果其他用户想要使用被创建的对象，必须要为其赋予相应的权限。

## 权限类型
- SELECT
- INSERT
- DELETE
- UPDATE
- TRUNCATE
- REFERENCES
- CREATE
- TEMPORARY
- TRIGGER
- CONNECT
- EXECUTE
- USAGE

#### 权限与用户
- **修改一个对象（数据库  or 表。。）的权限永远是该对象所有者独有的权限**
- 对于不同的用户，我们可以按照情况赋予对对象操作的不同的权限

- 对于超级用户或者对象的当前所有者来说，使用`alter`命令总可以将对象分配到一个新的所有者

#### 对象所有者的特殊权限
- 对于对象的所有者来说，它具备一些其他用户无法具备的特殊权限
	- `DROP`删除对象权限
	- `GRANT`赋予权限的权限
	- `REVOKE`撤销权限的权限
		- 对象所有者可以撤销自己的普通权限！

#### with grant option
- 对于普通用户来说，他可以将自己对一个对象的操作权限转授给其他人，这意味着他和被转授的用户都具备了对对象操作的相应权限。**一旦授权选项被撤销，所有通过这个操作获得权限的用户豆浆失去该权限**

#### 赋予权限
###### 1. GRANT赋予用户对表的指定操作权限
- `grant insert, select on userinfo to ziawang;`将对userinfo表的插入和查询权限赋予用户ziawang
- `grant all on userinfo to ziawang;`表示将userinfo表的所有操作的权限赋予用户ziawang


#### 撤销用户权限 
###### 1. REVOKE撤销用户对表的指定操作权限
- `revoke all on userinfo to mingming;`表示将对userinfo表的所有操作权限从mingming身上全部收回

## PUBLIC


## 权限组