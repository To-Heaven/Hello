# Thread
## threading.Thread实现模拟文本编辑器

> 主要是考虑到了线程的特性，共用一个文件

- 串行的执行任务

```python
from threading import Thread


class MyLittleNotepad(Thread):
    container = []
    format_container = []
    fp = open('notepad.txt', 'a')

    def run(self):
        while True:
            try:
                self.my_write()
                if self.container:
                    self.my_format()
                    if self.format_container:
                        self.flush_to_file()
            except Exception:
                break
        self.fp.close()

    def my_write(self):
        message = input('>>>').strip()
        self.container.append(message)

    def my_format(self):
        message = self.container.pop().upper()
        self.format_container.append(message)

    def flush_to_file(self):
        res = self.format_container.pop() + '\n'
        self.fp.write(res)
        self.fp.flush()


my_little_notepad = MyLittleNotepad()
my_little_notepad.start()
```


- 并发的执行任务

```python
from threading import Thread


class MyWrite(Thread):
    def __init__(self, container):
        super().__init__()
        self.container = container

    def run(self):
        self.my_write()

    def my_write(self):
        while True:
            message = input('>>>').strip()
            if not message: continue
            self.container.append(message)


class MyFormat(Thread):
    def __init__(self, format_container, container):
        super().__init__()
        self.format_container = format_container
        self.container = container

    def run(self):
        self.my_format()

    def my_format(self):
        while True:
            if self.container:
                message = self.container.pop().upper()
                self.format_container.append(message)


class MyFlush(Thread):
    filename = 'notepad.txt'

    def __init__(self, format_container):
        super().__init__()
        self.format_container = format_container

    def run(self):
        self.flush_to_file()

    def flush_to_file(self):
        with open(self.filename, 'a') as af:
            while True:
                if self.format_container:
                    res = self.format_container.pop() + '\n'
                    af.write(res)
                    af.flush()


container = []
format_container = []

my_write = MyWrite(container)
my_format = MyFormat(format_container, container)
my_flush = MyFlush(format_container)

my_write.start()
my_format.start()
my_flush.start()

```

## threading.Thread & threading.Event 实现模拟简单客户端连接服务端

```python

from threading import Thread, Event, get_ident
import time


def client_wait():
    count = 1
    while not e.is_set():
        if count > 3:
            raise ConnectionError('连接失败，请稍后尝试')
        print('%s 尝试第%s次连接' % (get_ident(), count))
        count += 1
        e.wait(1)
    print('%s  连接成功' % get_ident())


def server_start():
    print('server is starting . . . ')
    time.sleep(6)
    e.set()


if __name__ == '__main__':
    e = Event()
    for i in range(3):
        client = Thread(target=client_wait)
        client.start()
    server = Thread(target=server_start)
    server.start()

```





# 要问的要问的要问的要问的要问的要问的要问的要问的要问的要问的要问的要问的要问的要问的

import os
from multiprocessing import Process
# a = 1
def func():
    print(a, '来自于 %s'%os.getpid())

# 要问的要问的要问的要问的要问的要问的要问的要问的
# if __name__ == '__main__':
#     print('主进程%s开始'%os.getpid())
#     a = 1
#     func()
#     p = Process(target=func)
#     p.start()