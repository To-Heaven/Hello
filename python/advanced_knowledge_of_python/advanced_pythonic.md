## 如何pythonic——在保证阅读性的情况下使用更少的代码
> 阅读性是关键，代码数量是其次

[Python 有哪些优雅的代码实现让自己的代码更pythonic？](https://www.zhihu.com/question/37751951?sort=created)
- 充分提现python自身特色的代码风格
	- 变量交换使用packing/unpacking
	- 使用for而不是while遍历
	- 使用with上下文确保安全关闭文件描述符
	- 倒序用reserved而不是切片[::-1]
	- 列表推导式有时候用起来并不合适
		- 对于包含有复杂逻辑的列表推导式，不如使用for循环，而且能够更好的对异常使用try...except分支捕获处理
	- 生成器可以用来解决很多问题。
		- map和sorted要分清情况使用
	-   使用.format代替%s占位符，因为其可读性高

- PEP8编码规范/Google Python Style Guide
- 通读官方`language reference` 和`libiray reference`
- Falsk    gevent    requests    


## 不合理的变量名
1. 不使用简单的大小写区分不同的对象
2. 不重复使用已经存在上下文的变量名来表示不同的对象
3. 变量名不能与内建变量冲突
4. 变量名要与解决的问题一致
5. 较长的变量名有时候是必要的（便于理解阅读）

## 注释
- 块注释和行注释
	1. 仅仅用来注释复杂的操作、算法和难以理解的技巧或不够一目了然的代码
	2. 行注释与代码之间要有一段举例
	3. 块注释之后要有几行空行

- 文档注释
	- 给外部可访问的函数和方法添加注释即使其很简单
	- 格式如下

```python
def find_string_in_queue(strqueue, str):
	"""Find whether the special string is in the queue or not 
		Args:(parameter type, what used for )
			strqueue: string , string queue list for search
			str:string, string to find
	Returns:(return type, return value)
			boolean, secified string found return True, else False
	"""
	
	function body 
	. . .
```
	
- **注释的禁忌**
	- **注释用来解释代码共呢个，原因，想法而不是对代码本身解释**
	- 不能因为代码简单明了就不写注释
	- 不能用注释删除代码

## 空格
> 好的布局关系到代码的可扩展性和可维护性

- 保持上下文语义的易理解性
	- 上下两个函数相互关联的时候，不应该用空格隔开
- 一组代码表达完一个完整思路之后就应该空行空隔
	- 在满足上一条的情况下


## 函数
1. 函数中流程控制嵌套不能太多，最好不要超过3层
2. 函数参数设计要考虑向下兼容（考虑使用默认参数）
3. 保证函数功能的一致性（即尽量一个任务对应一个函数）
4. 不使用可变对象作为函数默认值（可变对象作为默认值在递归中比较有效）
5. 使用异常替换返回错误，保证通过单元测试
