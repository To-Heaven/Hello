## 使用multiprocess.dummy的正确姿势
- 我们有时候拿不准到底是使用多进程还是多线程来处理任务。处理这种情况，一个简单暴力的方法是使用这个模块实现'兼容模式'。看测试结果到底哪个更好就采用哪个
	- `multiprocessing.dummy`虽然是在多进程模块中的，但是它提供了和多线程基本相同的接口。
	- 官方原话
		- multiprocessing.dummy replicates the API of multiprocessing but is no more than a wrapper around the threading module.

```python
# from multiprocessing import Pool
from multiprocessing.dummy import Pool
```