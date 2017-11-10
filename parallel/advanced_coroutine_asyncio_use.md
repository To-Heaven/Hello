## asyncio 的同步机制


#### semaphore信号量
- 使用semaphore控制同时并发量，在线程中，semaphore用来控制并发量的同时为数据加锁

```python
import aiohttp
import asyncio

NUMBERS = range(20)
URL = 'http://httpbin.org/get?a={}'
sema = asyncio.Semaphore(5)


async def async_get(i):
    async with aiohttp.request('GET', URL.format(i)) as r:
        data = await r.json()
    return data['args']['a']

async def get_result(i):
    with (await sema):
        r = await async_get(i)
        print(f'get({i}) = {r}')


loop = asyncio.get_event_loop()
f = asyncio.wait([get_result(i) for i in NUMBERS])
loop.run_until_complete(f)
```


#### Lock锁

```python
import asyncio
import functools


def unlock(lock):
    print('callback releasing lock')
    lock.release()


async def test(locker, lock):
    print('{} waiting for the lock'.format(locker))
    with await lock:
        print('{} acquired lock'.format(locker))
    print('{} released lock'.format(locker))


async def main(loop):
    lock = asyncio.Lock()
    await lock.acquire()
    # lock.acquire()
    loop.call_later(2, functools.partial(unlock, lock))
    await asyncio.wait([test('l1', lock), test('l2', lock)])


loop = asyncio.get_event_loop()
loop.run_until_complete(main(loop))
loop.close()
```
