# python argparser模块

#### 为什么要使用argparser模块？
- argparser可以办我们很方便的解析命令行传递过来的参数，包括：
	1. 定义命令行可以传递那些参数
	2. 定义命令行传递过来的参数值的取值范围
	3. 将命令行传递过来的值封装起来
	4. and so on ....
- 如果我们不适用argparser模块，利用`sys.argv[1:]`也可以取到这些命令行的参数，但是对这些参数的处理，解析等都需要自己一步步完成，既然有现成呢个的工具argparser可以使用，为什么还要自己浪费时间去处理呢。


## 使用argparser
1. 首先你要实例化一个`ArgumentParser`对象

```python
import argparse


parser = argparse.ArgumentParser()
```


2. 然后通过`parser.add_argument()`方法来定制命令行参数传参的内容和规范，比如传递的值的类型，值的取值范围等
	- `help`用于说明参数的意义或使用帮助
	- `choices`用于限制参数值的取值范围
	- `type`用于限制参数值的类型
		- 当使用了`action`的时候，就不需要指定`type=bool`了
	- `action`
		- 对于一些参数值为布尔值的命令行参数，我们可以使用`action=store`来方便参数的调用，比如`add_argument('-n', action='store_true')`
			1. 当在命令行制定`-n`参数的时候，我们不需要提供参数值，argparser会自动的将`-n`的参数值解析为`True`
			2. 当在命令行不指定`-n`参数的时候，对应的，argparser就会将该`-n`的参数值解析成`False`

```python
parser.add_argument('-n', '--name', help='姓名', type=str)
parser.add_argument('-h', '--hobbies', help='爱好',
					choices=['singing', 'food', 'movies', 'travel'])
parser.add_argument('-handsome', action='store_true', help='帅不帅？')

```

3. 将命令行参数值封装
	- 这个命令会将获取的所有参数值封装到一个`NameSpace`对象中，我们可以通过调用属性的方式调用命令行传递过来的参数
		- 比如`arg.n`或者`arg.name`可以获取`-n`传递过来的参数值
```python
args = parser.parser_args()

```










