# 进程

## multiprocessing.Process创建进程的两种方式（简单爬虫为例）
- 使用multiprocessing.Process()创建进程

```python

import requests
from multiprocessing import Process, Lock
import os

urls = [
    'http://www.ziawang.com/',
    'https://dev.mysql.com',
    'https://coolshell.cn/',
    'https://www.joinquant.com',
    'http://www.python-requests.org'
]


def get_page(url):
    response = requests.get(url)
    if response.status_code == requests.codes.ok:
        return response


def parser_page(url):
    response = get_page(url)
    filename = str(hash(response.url))
    with open(filename, 'wb') as fb:
        headers = response.headers
        fb.write(bytes(str(headers), encoding='utf-8'))
        for chunk in response.iter_content(1024):
            fb.write(chunk)
        else:
            print('%s :parser successful'%response.url)


if __name__ == '__main__':
    p_l = []
    for i in range(len(urls)):
        p = Process(target=parser_page, args=(urls[i],))
        p_l.append(p)
        p.start()
    for p in p_l:
        p.join()
    print('任务完成')


```

- 创建Process子类的方法实现多进程

```python
import requests
from multiprocessing import Process


class Mylittlespider(Process):
    parser_page_per_size = 1024
    ecoding = 'utf-8'

    def __init__(self, url):
        super().__init__()
        self.response = requests.get(url)
        if not self.response:
            raise TimeoutError('request failed')

    def run(self):
        self.parser_page()

    def parser_page(self):
        filename = str(hash(self.response.url))
        headers = self.response.headers
        with open(filename, 'wb') as fb:
            for item in headers.items():
                fb.write(bytes('%s : %s \n' % item, encoding=self.ecoding))
            for chunk in self.response.iter_content(self.parser_page_per_size):
                fb.write(chunk)
            else:
                print('%s parser successful ' % self.response.url)


urls = [
    'http://www.ziawang.com/',
    'https://dev.mysql.com',
    'https://coolshell.cn/',
    'https://www.joinquant.com',
    'http://www.python-requests.org'
]

if __name__ == '__main__':
    for url in urls:
        p = Mylittlespider(url)
        p.start()

```

>  但是这种爬虫方式太低效了，每一个进程内是存在I/O阻塞（网络IO和文件读写IO）的，并且进程内的过个任务之间是串行的，每一个parser方法都必须等到get_page之后才能运行，使用进程池看看


## multiprocessing.Pool实现多进程
