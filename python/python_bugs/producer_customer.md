1. 

```python
from multiprocessing import Process, JoinableQueue

def producer(q):
    for i in range(10):
        q.put(i)
    q.join()



def customer(q):
    res = q.get()
    print(res)
    q.task_done()

if __name__ == '__main__':
    q = JoinableQueue(10)
    p = Process(target=producer, args=(q, ))
    c = Process(target=customer, args=(q, ))
    p.start()
    c.start()
    p.join()
    print('这是主进程')
```

2. 

```python
from multiprocessing import Process, JoinableQueue
import time
import random
import os

q = JoinableQueue(5)
def producer():
    for i in range(10):
        time.sleep(random.random())
        q.put(i)
        print('producer:%s 生产%s'%(os.getpid(), i))
    q.join()


def customer():
    while True:
        time.sleep(2)
        res = q.get()
        print('customer:%s 消费%s'%(os.getpid(), res))
        q.task_done()

if __name__ == '__main__':
    p_l = []
    for i in range(3):
        p = Process(target=producer)
        p_l.append(p)
        p.start()
    for j in range(3):
        c = Process(target=customer)
        c.daemon = True
        c.start()
    for p in p_l:
        p.join()
    print('这是主程序')

```

2. 

```python
from multiprocessing import Manager, Process


class Producer:
    def __init__(self, q):
        self.q = q

    def run(self):
        self.produce()

    def produce(self):
        for i in range(10):
            self.q.put(i)
            print('produce %s ' % i)
        self.q.join()


class Customer:

    def __init__(self, q):
        self.q = q


    def custom(self):
        while True:
            res = self.q.get()
            print('custom %s' % res)
            self.q.task_done()


if __name__ == '__main__':

    with Manager() as m:
        q = m.JoinableQueue(3)
        p = Producer(q)
        c = Customer(q)
        p_process = Process(target=p.produce)
        c_process = Process(target=c.custom)
        p_process.start()
        c_process.start()
        print('主进程')

```
