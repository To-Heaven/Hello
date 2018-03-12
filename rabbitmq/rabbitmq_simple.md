# 简单模式


#### 简单模式原理
简单模式非常简单，在简单模式中不会使用exchange(交换机)，但是有一个注意的细节:
- 当接收方接收到信息之后，此时消息队列中的信息就被取出，不再存在于队列中了，这个时候如果在接收方进程在处理消息任务的过程中出现了异常，意味着这个任务将执行失败，最重要的是**消息队列中该消息数据也已经不复存在，想重新执行该任务也执行不了，除非发送方再次给你发送一个相同的信息**。但是那样也太麻烦了，而且耦合性也比较强，为了解决这个问题，pika模块中，**接收方**在使用`basic_consume`接收到该消息的时候，声明参数`no_ack=False`即可，当接收方正常处理完该任务之后，就会下个rabbitMQ服务器返回一个**应答信息**，当rabbitMQ接收到这个应答之后才会清除队列中的消息



## pika模块
- 在python中我们可以通过pika模块来操作消息队列。

#### 创建链接
- 在操作消息队列之前肯定要连接rabbitMQ服务端，返回值为一个connection对象，我们可以使用这个connection对象来创建一个channel(频道)对象，通过这个频道对象来创建和操作消息队列

```python
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters(host='172.96.203.6',credentials=pika.PlainCredentials(
        username='ziawang',
        password='手动马赛克'
    ))
)
```


#### 创建一个消息队列
- 参数
	- queue:要创建的队列的名称，一个rabbitMQ服务器上可以有很多个队列
	- durable:设置为True的时候，表示要将消息队列进行持久化，同时还需要在发送信息的时候在`properties`参数中指定模式
		- `properties=pika.BasicProperties(delivery_mode=2) `消息持久化模式

```python
channel.queue_declare(queue='ziawang')
```

#### 将消息推送到队列中
- 参数
	- exchange：使用的交换机，有下面四种类型
		- `''`，空字符串代表简单模式
		- `'fanout'`，使用发布订阅模式
		- `'direct'`，使用关键字模式
		- `'topic'`，模糊模式
	- routing_key：要将消息推送到哪一个队列
	- body：要推送的数据内容	

```python
channel.basic_publish(
    exchange='',    # 不是用交换机
    routing_key='ziawang',  # 不适用交换机ide时候，touting_key为队列名称
    body='hello ziawang'
)
```


#### 从消息队列中取出数据
- 参数
	- consumer_callback：取出数据之后要立即执行的回调函数，这个回掉函数有四个参数
		- ch：表示接收者对应的channel对象
		- method：如果在`basic_consumer`中指定的`no_ack=False`，需要在这个回掉函数中调用`channel_obj.basic_ack(delivery_tag=method.delivery_tag)`
		- properties：这里面存放了发送方发送时在`properties中`中存放的数据
		- body：从消息队列中取到的值

```python
def callback(ch, method, properties, body):
    print(body)                                 # 接受到的body中时bytes数据
    ch.basic_ack(delivery_tag=method.delivery_tag)


channel.basic_consume(      # start_consuming之后，如果对类中有数据才会执行，如果没数据就会等待直到数据到来时立即执行
    consumer_callback=callback,     # 取出数据之后立即执行callback
    queue='ziawang',
    no_ack=False
)
```

#### 接收方开始监听消息队列中的信息

```
channel.start_consuming()
```


#### 发送断开链接

```
connection.close()  # 关闭链接
```


#### 设置接收方获取队列中消息的方式
- 默认情况下，消息队列中的数据是按照顺序来被接收方取走的，例如：消费者1 去队列中获取`奇数`序列的任务，消费者1去队列中获取`偶数`序列的任务。
- 参数
	- prefetch_count=1:表示谁先处理完自己的任务来取消息的时候就将消息交给谁，这种方式处理消息效率高

```python
channel.basic_qos(prefetch_count=1)
```

## 代码实现
#### 1. 简单的生产者消费者
- 发送端

```python
connection = pika.BlockingConnection(pika.ConnectionParameters(
        host='172.96.203.6'))
channel = connection.channel()
 
channel.queue_declare(queue='ziawang')
 
channel.basic_publish(exchange='',
                      routing_key='ziawang',
                      body='www.ziawang.com')
print(" sent www.ziawang.com ")
connection.close()
```

- 接收端

#### 2.  使用ack应答保证异常情况下数据不丢失
- 发送端

```python
connection = pika.BlockingConnection(pika.ConnectionParameters(
        host='172.96.203.6'))
channel = connection.channel()
 
channel.queue_declare(queue='ziawang')
 
channel.basic_publish(exchange='',
                      routing_key='ziawang',
                      body='www.ziawang.com')
print(" sent www.ziawang.com ")
connection.close()
```

- 接收端

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(
        host='172.96.203.6'))
channel = connection.channel()

channel.queue_declare(queue='ziawang')

def callback(ch, method, properties, body):
    print(" Received data: %r" % body)
    ch.basic_ack(delivery_tag = method.delivery_tag)	# 应答

channel.basic_consume(callback,
                      queue='ziawang',
                      no_ack=False)

print('  Waiting for messages')
channel.start_consuming()

```

#### 3. 消息队列持久化
需要做两件事儿
1. 不论是接收方还是发送方，在声明创建队列的时候，指定durable参数为True
2. 在`basic_publish`发送数据的时候需要指定`properties`参数的值

```python

channel.basic_publish(exchange='',
                      routing_key='ziawang',
                      body='Hello ziawang!',
                      properties=pika.BasicProperties(
                          delivery_mode=2, # 持久化
                      ))
```

- 发送端

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='172.96.203.6'))
channel = connection.channel()

# make message persistent
channel.queue_declare(queue='ziawang', durable=True)

channel.basic_publish(exchange='',
                      routing_key='ziawang',
                      body='Hello ziawang!',
                      properties=pika.BasicProperties(
                          delivery_mode=2, # 持久化
                      ))
print("   Sent Data")
connection.close()
```

- 接收端

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='172.96.203.6'))
channel = connection.channel()

# 持久化
channel.queue_declare(queue='ziawang', durable=True)


def callback(ch, method, properties, body):
    print(" Received data: %r" % body)
    ch.basic_ack(delivery_tag = method.delivery_tag)

channel.basic_consume(callback,
                      queue='ziawang',
                      no_ack=False)

print('   Waiting for messages ')
channel.start_consuming()
```






#### 4. 更改获取消息方式
- 接收方


```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='172.96.203.6'))
channel = connection.channel()

# 持久化
channel.queue_declare(queue='ziawang', durable=True)


def callback(ch, method, properties, body):
    print(" Received data: %r" % body)
    ch.basic_ack(delivery_tag = method.delivery_tag)

channel.basic_qos(prefetch_count=1)

channel.basic_consume(callback,
                      queue='ziawang',
                      no_ack=False)

print('   Waiting for messages ')
channel.start_consuming()
```







































