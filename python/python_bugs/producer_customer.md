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