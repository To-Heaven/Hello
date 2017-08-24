## 单词
pseudo-random	伪随机的  <br>
distribution		分配   <br>
uniform 			均一的规格一律的   <br>
permutation		序列，排列   <br>
sample			抽样   <br>
replacement		代替   <br>
compute			计算   <br>
lognormal		对数正态的   <br>
negative			消极的，负的   <br>
exponential		指数   <br>
semi-open		半开口 <br>
deterministic		确定性的 <br>
extensive		广泛的 <br>
extence			存在，实体 <br>
cryptographic	用密码写的 <br>
supply			提供 <br>
hidden 			神秘的 <br>
devising			设计 <br>
override			覆盖 <br>
internally		内地，本质的 <br>
permutations		序列，排列 <br>
imply			暗示 <br>
sample			样品 <br>
## introduction of random class
- class Random can also be subclass if you wanted to as a different basic generator of your own devising: in that case ,override the random() , seed(), getstate(), and setstate() methods. optionally , a new generator can supply a getrandbits() method------this allows randrange() to produce selecttions over an arbitrarily large range

> 注意： the pseudo-random generators of this module **should not be used for security purposes**. （可以参考secrets模块)

## random.Method
### Functions for integers
- random.randrange(stop)
- radom.randrange(start, stop[step])
	- return a randomly selected element from range(start, stop, step).this is equivalent to choice(range(start, stop, step)), but doesn't actually build a range object
	- the positional argument pattern matchs that of range().keyword arguments should not be used beause the function may be use them in unexpect ways.

```python
>>> import random
>>> random.randrange(10)
8
>>> random.randrange(4, 9, 2)
8
>>> random.choice(range(4, 9, 2))
4
>>> 
```

- random.randint(a, b )
	- return a random integer n such that a<= n <= b。即n在区间[a, b]中，等价于 random.randrange(a, b+1)

```python
>>> random.randint(1, 3)
2
# 等价于
>>> random.randrange(1, 4)
1
>>> random.randrange(1, 2)
1
>>> random.randint(1, 2)
1
>>> random.randint(1, 2)
1
>>> random.randint(1, 2)
1
>>> random.randint(1, 2)
2
>>> random.randint(1, 2)
2
>>> random.randint(1, 2)
1
>>> random.randint(1, 2)
2
>>> random.randint(1, 2)
1
>>> 
```
 
### functions  for   sequences
- random.choice(sequence)
	- return a element of sequence ,the sequence must not be empty, otherwise raise IndexError

```python
>>> l = [1,2,3,4]
>>> s = []
>>> random.choice(l)
3
>>> random.choice(s)
Traceback (most recent call last):
  File "D:\Python36\lib\random.py", line 255, in choice
    i = self._randbelow(len(seq))
  File "D:\Python36\lib\random.py", line 232, in _randbelow
    r = getrandbits(k)          # 0 <= r < 2**k
ValueError: number of bits must be greater than zero

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<pyshell#58>", line 1, in <module>							# 为毛爆出两个错。。。。
    random.choice(s)
  File "D:\Python36\lib\random.py", line 257, in choice
    raise IndexError('Cannot choose from an empty sequence')
IndexError: Cannot choose from an empty sequence
>>> 
```

- random.choices(population, weights=None,*,cum_weights=None, k=1)
	- return a k sized listed of elements chosen fron the population with replacement.if the population is empty , raise IndexError
	- weights相对权重列表 和 cum_weight累计权重列表不能同时存在
	- 可以返回population中重复的值
	- Note: population should support indexing, if the set object implied, raise TypeError  

```python
>>> l = [1,2,3,4,5,6,7,8,9, 0]
>>> random.choices(l, k=5)
[7, 1, 8, 0, 6]
>>> random.choices(l, k=5)
[7, 1, 8, 7, 7]
>>> s = {1,2,3,5,6,7,8,9,4}
>>> random.choices(s, k = 4)
Traceback (most recent call last):
  File "<pyshell#62>", line 1, in <module>
    random.choices(s, k = 4)
  File "D:\Python36\lib\random.py", line 352, in choices
    return [population[_int(random() * total)] for i in range(k)]
  File "D:\Python36\lib\random.py", line 352, in <listcomp>
    return [population[_int(random() * total)] for i in range(k)]
TypeError: 'set' object does not support indexing
>>> 
```

- random.shuffle(sequence[random])
	- return None.but shuffle sequence in place
	- to shuffle an immutable sequence and return a new shuffle list,use sample(sequence, k=len(sequence)) instead
	- 

```python
>>> l = [1,2,3,4,5,6,7,8,9]
>>> random.shuffle(l)
>>> l
[7, 8, 2, 1, 4, 3, 6, 5, 9]
>>> 
```

- random.sample(population, k)
	- return a k length of unique element chosen form the population sequence or **set**.
	- 返回不重复的值
	- Members of the population need not be hashable or unique .if the population contains repeats. then each occurence is a selection in the sample

```python
>>> l = [1,1,1,1,1,1]
>>> random,sample(l, k =4)
Traceback (most recent call last):
  File "<pyshell#64>", line 1, in <module>
    random,sample(l, k =4)
NameError: name 'sample' is not defined
>>> random.sample(l, k=4)
[1, 1, 1, 1]
>>> 
```

	- To choose a sample from a range of intergers , use a range() object as an argument . this is especially fast and space efficient for sampling from a large population: sample(range(1000000000), k = 60)

    
```python
>>> l = [1,2,3,4,5,7,6]
>>> 
>>> random.sample(l, 4)
[4, 3, 7, 2]
>>> random.sample(l, 4)
[1, 7, 2, 4]
>>> 
```

- random.random()
	- return the next random floating point number in the range[1.0, 1.0)

- random.uniform(a,b)
	- get a random number in the range  [a, b) or [a, b]  depanding on the rounding

```python
>>> random.uniform(1, 2)
1.294780638360937
>>> random.uniform(1, 2)
1.1008676965275115
>>> random.uniform(1, 2)
1.5602883039708386
>>> random.uniform(1, 2)
1.8171061666462776
>>> random.uniform(1, 2)
>>> random.uniform(1, 2)
1.1989541905877221
>>> random.uniform(1, 2)
1.4194695033161044
>>> random.uniform(1, 2)
1.6234092806615703
>>> random.uniform(1, 2)
1.8482188686791532
>>> random.uniform(1, 2)
1.7251909919015405

``` 







