## 包导入的方式与要注意的问题
- 绝对导入
	- 包内模块之间导入应使用绝对导入
		- `from package.sub_package import module_name`
	- 即从最顶层的包开始向下导入
		- 导入时注意import的右边不能有包的名称
- 相对导入
	- 即使用`.`  `..` 分别代表当前目录和上层目录
	- **必须明确一点：相对导入时，模块不能作为主程序的入口**。如果将存在相对导入语句的模块当作主程序运行了，那么该模块所在的文件夹就不能视为一个包。
	- 那么问题来了，python中什么是主程序？
		- 主程序在python中没有明确指出，在java等其他语言中均有主程序存在。在python中，我们所说的主程序实际上就是程序运行时的主要脚本文件，比如每个程序都有一个start.py文件，那么程序开始运行必然是从该脚本开始。那么这个脚本上运行的程序就是主程序。
	- 相对导入时，被导入对象的模块不能作为主程序的入口，就是该模块不能当作主程序来运行。但是，并不是说相对导入就没有用了，只要该模块不是主程序所在的模块，我们就可以通过相对导入来导入包中的任意模块到当前模块中来。

``` python

|-----main.py				# from A.B  import  b1
A
|-----__init__.py 
|
------B
|	|-----b1.py			# from . import b2 ; print('in  b1')
|	|-----b2.py			# from ..C import c ; print('in b2')
|	|-----__init__.py	
|
-------C
	|-----c.py				# print('in c')
	|-----__init__.py		
```