# Websocket 介绍
 

## 相关概念
#### 背景
为什么要使用轮询/长轮询/websocket？
- 在Web前后端交互的过程中，对于前端页面，有一些数据他是需要实时更新的，比如聊天室（需要实时获取别人发送的消息），投票（需要获取实时投票的情况），要想实现数据的实时更新，方法有很多，比如让前端页面通过`window.setinterval()`每隔一段时间就向服务端发起一个请求来获取数据，这种方式就是轮询，还可以通过websocket建立客户端和服务端之间的TCP链接，通过socket套接字来实现数据的实时更新

#### Ajax轮询
什么是Ajax轮询？
- Ajax轮询就是客户端不断的每个一小段时间向服务端获取数据，不管服务端是否返回了客户端想要的数据，请求都会不断的发送，每一次轮询发起的请求都会与服务端通过TCP建立一次链接，服务端返回响应之后断开连接（HTTP1.0）。
- 我们可以使用`window.setInterval()`来实现轮询


轮询的通讯方式特点
0. 基于HTTP协议，因此链接是**单向的**，服务器不会主动向客户端推送数据
1. 不断的向服务端发送请求，并且请求是**单向的**
2. 不管上一次请求有无结果返回，下一次的请求仍然会定时发出
3. 每一次发起请求建立的TCP链接在服务端返回响应或超时的情况下会断开


轮询有什么缺点？
1. 客户端不断发送的这些请求中，大部分时没法获取到所需要的数据的，会浪费带宽
2. 如果客户端数量非常庞大，如果每一个客户端定时Ajax轮询，不断向服务端发送请求，服务端将承受客户端海量请求的巨大压力
3. **数据有可能会在两次轮询发送请求的时间间隔之间到达，意味着客户端将会延迟收到响应结果**


Ajax轮询怎么实现？

```javascript
var xhr = new XMLHttpRequest();
    setInterval(function(){
        xhr.open('GET','/');
        xhr.onreadystatechange = function(){
			// .....
        };
        xhr.send();
    },1000)

``` 




#### Ajax长轮询
什么是Ajax长轮询？
- Ajax长链接即客户端向服务端通过Ajax发送一个请求（，并为这次链接设置一个链接时间）服务端获取请求之后，**会将这个请求挂起（即保持长链接）**直到返回响应数据或者链接超时，当客户端接收到服务端的响应值之后，会**立即再次发送一个请求重复上述过程**。
 

长轮询的通讯方式特点
0. 基于HTTP协议，因此链接是**单向的**，服务器不会主动向客户端推送数据
1. 客户端必须等待请求返回或者上一次请求超时才能发起下一个请求



长轮询与轮询的区别（有点类似同步和异步）
1. 长轮询采取的是`阻塞模型`，在请求获取到响应数据或者到达超时时间之前，请求会被挂起（即保持长链接），直到服务端返回响应或超时，并且在服务端返回响应或链接超时后立马再次发送一个新的请求。
2. 轮询是不断有间隔的向服务端发起请求，服务端接收到请求之后不管有没有满足响应的数据都会返回。
3. **如果数据实时更新非常的频繁，那么这两种方式则没有区别。**


长轮询有什么缺点？
1. 客户端必须等待请求返回或者上一次请求超时才能发起下一个请求，显著的增加了延迟
2. 长轮询需要很高的并发。

Ajax长轮询怎么实现？


```javascript
   function ajax(){
        var xhr = new XMLHttpRequest();
        xhr.open('GET','/user');
        xhr.onreadystatechange = function(){
              ajax();	
        };
        xhr.send();
    }
```




#### websocket
websocket是什么鬼？
- websocket是一个基于TCP协议（和HTTP协议）的应用层协议，通过websocket我们可以实现里浏览器与服务端的全双工通信。所谓全双工通信，就是指数据的发送方在发送数据的同时也能够接收数据，也就是说浏览器发送请求和接收响应是可以同时进行的，他本质上是再服务端与客户端之间通过socket套接字建立了一个TCP双向链接。
- 只要客户端与服务端支持websocket协议并建立了链接，对于数据需要实时更新的业务场景，服务端就可以在数据准本好的情况下主动的将数据从服务端推送到客户端。HTTP协议他是一个实现了单工通信的协议，每一次只能有客户端想服务端发送请求并建立链接，然后服务端返回响应之后就会断开连接，服务端不会主动向客户端推送消息。


单工通信、半双工通信、全双工通信
- 单工通信是指数据只能够从一方发送给另一方的单向传输
- 半双工通信是指虽然也可以实现发送方即可以发送数据也可以接收数据，但是发送数据可接收数据这两个过程必须轮流交替的进行。就好比一条很窄的马路，每次只能够以两者通过，现在有两辆车相向而行，意味着一辆车只能等另一辆车通过之后，他才能再从这条路通过。
- 双全工是指数据的发送方在发送数据的同时也能够接收数据，也就是说浏览器发送请求和接收响应是可以同时进行的。



为什么要使用websocket？
- 在需要实时更新的Web页面中，比如投票和Web聊天室，传统的轮询和长轮询这种基于HTTP协议的方式一方面会带来带宽的浪费和服务端资源的浪费，另一方面它只能实现客户端到服务端之间的单项通信，使用websocket建立的双向通信就可以解决客户端与服务端为了维持数据同步而发起的无效请求，当服务端数据准备好之后，就会主动地将数据推送给客户端。


```
GET /chatsocket HTTP/1.1
Host: 127.0.0.1:8002
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Origin: http://localhost:63342
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: mnwFxiOlctXFN/DeMt1Amg==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

websocket进行通讯的原理
- websocket是基于TCP和HTTP协议的一种协议，之所以说它基于HTTP，是因为
	1. 客户端浏览器首先需要通过HTTP请求完成一次握手过程建立链接，在HTTP请求头`Sec-WebSocket-Key`中向服务端发送一条随机的字符串
	2. 服务端在经过请求头解析之后获取到这段随机字符串，然后将`Sec-WebSocket-Key`这段随机字符串与`magic string`拼接在一起，使用他本地的websocket协议进行加密（`sha1`(hmac1)和`base64`）之后，将加密后的这段数据通过响应头`xxxx`发送给客户端
		- `magic-string`：是一个相互约定好的、不能改变的、用来给`Sec-WebSocket-Key`进行加密的字符串
			- 他的值是固定的，永远为`258EAFA5-E914-47DA-95CA-C5AB0DC85B11`
	3. 客户端浏览器接收到响应之后，会解析响应头，从响应头中获取这个加密之后的字符串，同时客户端还会根据它本地的websocket协议对最开始它发送的未加密随机字符串也进行一次加密操作（`sha1`和`base64`），如果加密之后的数据与从服务端接收到的加密字符串是相同的，就会成功建立连接，建立了链接之后两者就会进行相互通信了。

- websocket下协议中的客户端和服务端双全工通讯过程中数据的收发有一个数据的`封包`和`解包`过程，在客户端客户端，浏览器已经将封包和解包的封装到JavaScript类库中，因此socket服务端我们需要手动实现数据的`封包`和`解包`(可以尝试画出websocket数据包的结构)
	- 数据的封包。假设现在由服务端向客户端发送一条数据
		0. 
		1.  
	- 数据的解包。假设现在客户端向服务端发送一条数据
		0. 服务端接收到的数据主要分成三个部分，分别是opcode、payload len、扩展pay—load len+data。根据**payload len值的不同，服务端接收到的数据也是不同的**
		1. 服务端接收到数据之后，要先获取`payload len`部分，这个部分占了`7个bit位`，因此它能够表示的最大值是127
			- 如果payload len表示的长度小于等于125，那么payloadlen的长度不需要扩展，也就是说不会用到extended payload length
			- 如果payload len等于126，那么就会向下读取`16bit位`的extended payload length
			- 如果payload len等于127，那么就会向下读取`64bit位`的extended length
		2. 如果设置了mask，即mask的值为1，那么就继续向下读取`32bit位`，者`32bit位`的数据就作为`MASKING KEY`，服务端通过这个将data数据与`MASKING KEY`进行位运算，最终获取解包后的数据


```
fin(1)：告诉接收端这是否是这段message中的最后一块(message数据会被分块发送)，如果是0，表示不是最后一条，服务端会继续保持监听状态去等待message的其它部分，否则服务端就会认为这段message接收完成了
rsv(3)
opcode(4)：定义解释器如何解释payload data，比如0x1表示文本，0x2表示二进制
mask(1)：告诉服务端这个message是否经过编码，他总是1，服务端可以忽略这个值
payload len(4)
Extended payload length （64）
payload data
```

```JavaScript
// 通过masking key和data进行解包
var DECODED = "";		// 最终解包获得的数据
for (var i = 0; i < ENCODED.length; i++) {
    DECODED[i] = ENCODED[i] ^ MASK[i % 4];
}

// mask：masking key的十进制值
// encoded：data数据
```


服务端socket握手过程代码实现

```python
import socket
import base64
import hashlib
 
def get_headers(data):
    """ 将请求头格式化成字典
	Args:
		请求头中解析得到的Sec-WebSocket-Key随机字符串
    """

    header_dict = {}
    data = str(data, encoding='utf-8')
 
    for i in data.split('\r\n'):
        print(i)
    header, body = data.split('\r\n\r\n', 1)
    header_list = header.split('\r\n')
    for i in range(0, len(header_list)):
        if i == 0:
            if len(header_list[i].split(' ')) == 3:
                header_dict['method'], header_dict['url'], header_dict['protocol'] = header_list[i].split(' ')
        else:
            k, v = header_list[i].split(':', 1)
            header_dict[k] = v.strip()
    return header_dict
 

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind(('127.0.0.1', 8002))
sock.listen(5)
 
conn, address = sock.accept()
data = conn.recv(1024)
headers = get_headers(data) # 提取请求头信息
# 对请求头中的sec-websocket-key进行加密
response_tpl = "HTTP/1.1 101 Switching Protocols\r\n" \
      "Upgrade:websocket\r\n" \
      "Connection: Upgrade\r\n" \
      "Sec-WebSocket-Accept: %s\r\n" \
      "WebSocket-Location: ws://%s%s\r\n\r\n"
magic_string = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11'
value = headers['Sec-WebSocket-Key'] + magic_string
ac = base64.b64encode(hashlib.sha1(value.encode('utf-8')).digest())
response_str = response_tpl % (ac.decode('utf-8'), headers['Host'], headers['url'])
# 响应【握手】信息
conn.send(bytes(response_str, encoding='utf-8'))
```


python实现解包和封包(部分)

```python
info = conn.recv(8096)

payload_len = info[1] & 127
if payload_len == 126:
    extend_payload_len = info[2:4]
    mask = info[4:8]
    decoded = info[8:]
elif payload_len == 127:
    extend_payload_len = info[2:10]
    mask = info[10:14]
    decoded = info[14:]
else:
    extend_payload_len = None
    mask = info[2:6]
    decoded = info[6:]

bytes_list = bytearray()
for i in range(len(decoded)):
    chunk = decoded[i] ^ mask[i % 4]
    bytes_list.append(chunk)
body = str(bytes_list, encoding='utf-8')
print(body)
```



































