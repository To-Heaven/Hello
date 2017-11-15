## json
#### 介绍
- json是一种独立于编程语言之外的一种**数据交换格式**。json是一种资料交换语言，易于阅读，但是当内部嵌套层数较深时，我们会陷入复杂的数据节点查找中。
	- 不同语言编写的程序之间如果想要共享一段数据，那么就必须有一个可以被多种语言共同使用或者可以返序列化成自身语言中所拥有的数据结构，json就是这种数据结构，类似的还有xml


#### MIME类型
- `application/json`

#### 文件扩展名
- `.json`


## json 与 xml
#### 相同点 
- 都是一种数据转换格式


#### 不同点 
- json在数据交换时可以大大节省传输数据占用的带宽



## json语法规则
###### 数据结构规则
1. 数据存放在键值对中
2. 对象用大括号包含
3. json数组中的数据用中括号包含
4. 键值对之间用逗号隔开

###### 文件中的json对象
- 只要文件中更的内容满足的json的数据结构规则，那么就可以被反序列化


#### 键值对规则
- 键
	- json对象内的key必须用双引号包含
	- json字符串中的非数值键值必须用双引号包含
- 值
	- json对象内的value只能是以下类型
		- 数值: 0-9的数值集合，可以是负数或小数，也可以用e或E表示指数形式
		- 布尔值(true或false)
		- 字符串: 字符串必须在双引号中
		- 数组
		- 对象(包括json对象)
		- null


## json的两种数据结构形式
- 在我们开发过程中，通常要把数据转化成json格式，这里的意思就是把数据序列化成json字符串


#### json对象
###### json对象结构
- json对象由`{}`包含起来，内部是一个个key-value键值对，key是由双引号包含起来的字符串，value值可以是字符串、有序列表、数值、对象、null、布尔值(true/false)


```
{
      "firstName": "ziawang",
      "lastName": "haha",
      "sex": "male",
      "age": 23,
      "address": 
      {
         "streetAddress": "21 2nd Street",
          "city": "BeiJing",
         "state": "xxx",
          "postalCode": "xxxxx"
      },
      "phoneNumber": 
      [
          {
            "type": "home",
            "number": "xxxxxxxxxx"
          },
          {
            "type": "fax",
            "number": "xxxxxxxxxx"
          }
      ]
}
```



#### json数组数据结构
- json数组有`[]`包含起来，内部是一个个value组成，value值的类型同json对象中value值的类型


```
[
	{
	     "text":"This is the text","color":"dark_red","bold":"true","strikethough":"true","clickEvent":
	          {"action":"open_url","value":"zh.wikipedia.org"},
	     "hoverEvent":
	          {"action":"show_text","value":
	               {"extra":"something"}
	          }
	},
	{
	     "translate":"item.dirt.name","color":"blue","italic":"true"
	}
]
```

## json数据结构的应用
#### Web开发
- json格式最初就是用于Web开发中进行数据的交换，Java、JavaScript以及Node.js中主要使用json，而在php、c#中主要还是在使用xml作为数据交换的格式


#### NoSQL数据库
- 基于文档存储数据的NoSQL数据库如MongoDB、CouchDB、RavenDB等都是以json对象作为其基本数据存储格式


## json数据格式的检测
- [bejson](https://www.bejson.com/)


## messagePack
- messagePack是一种二进制序列化格式的数据，它可以像json一样在多种编程语言之间转换数据，比json占用更小的存储空间，目前messagePck已经对大部分主流语言都支持，在python中我们可以使用`msgpack-python`来及进行序列化

- python中安装msgpack`pip3 install msgpack-python`
- [json与messagePack分析](http://www.heyues.com/messagepack/)
- 使用msgpack

```python
>>> import msgpack
>>> msgpack.packb([1, 2, 3])
'\x93\x01\x02\x03'
>>> msgpack.unpackb(_)
[1, 2, 3]
>>> msgpack.unpackb(b'\x93\x01\x02\x03', use_list=False)
(1, 2, 3)
```

## json对象的跨站存取问题
- 利用json对象的跨站存取实现的常见恶意攻击比如CSRF，它利用服务端对客户端浏览器的信任来伪造客户端浏览器的请求实现攻击。JavaScript中使用的"沙盒"机制，使得JavaScript解释器只能从同一个站点来读取代码，提高了安全性
- 更多关于csrf及沙河机制，[点击跳转]()