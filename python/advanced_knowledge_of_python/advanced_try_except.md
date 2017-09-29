
## 断言assert
- assert语法格式
	- `assert expression1 [, expression2]`
	- expression1 返回值为False就会引发AssertionError
	- expression2 可选，存放触发AssertionError之后输出的错误信息
- 断言的用途
	- 捕获用户定义的约束而不是捕获程序的错误

- 缺点
	- 对性能有一定影响。因此在运行脚本时，命令行加上`-O`即`python -O test.py`就可以让解释器忽略脚本中与断言相关的语句

## 使用原则
- 应在这些情况下使用
	1. 函数调用后，确认函数返回值是否合理
	2. 判断一个逻辑条件，当这个条件对程序接下来的运行起到决定性作用时（即条件为必要条件）

- 这些情况下不能使用
	1. 不能滥用。断言应该使用在正常逻辑不能到达的地方或者正常总是返回True的场合
	2. 对于python解释器本身能够抛出Error，辅助我们处理错误的情况下就不能使用断言。
	3. 不要用断言进行用户交互输入检查
	4. try...except中不要使用断言


## 特别注意 
- 不要在try...except中使用assert语句，这样可能会导致无效的测试

```python
import unittest


class TestTryAssert(unittest.TestCase):
    def setUp(self):
        pass

    def test1(self):
        self.assertEqual(1, 2)

    def test2(self):
        try:
            self.assertEqual(1, 2)
        except Exception as e:
            print('find a exception ', e)

    def test3(self):
        try:
            self.assertEqual(1, 2)
        except Exception as e:
            print('find a exception ', e)
            self.assertEqual(1, 2)

    def tearDown(self):
        pass


if __name__ == '__main__':
    unittest.main()
# ------------------------------------------
C:\Users\Zia Wang>python3 D:\files\python_practice\0918-0924\0921\test.py
Ffind a exception  1 != 2
.find a exception  1 != 2
F
======================================================================
FAIL: test1 (__main__.TestTryAssert)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "D:\files\python_practice\0918-0924\0921\test.py", line 14, in test1
    self.assertEqual(1, 2)
AssertionError: 1 != 2

======================================================================
FAIL: test3 (__main__.TestTryAssert)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "D:\files\python_practice\0918-0924\0921\test.py", line 24, in test3
    self.assertEqual(1, 2)
AssertionError: 1 != 2

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "D:\files\python_practice\0918-0924\0921\test.py", line 27, in test3
    self.assertEqual(1, 2)
AssertionError: 1 != 2

----------------------------------------------------------------------
Ran 3 tests in 0.002s

FAILED (failures=2)			# test2测试竟然通过了，这是try..except的异常处理机制导致的
```

## 异常处理中需要注意的问题
1. 错误本身也是一个类，except捕获错误的时候，要注意错误类型的继承顺序。如果父类错误类型在子类错误类型前面，那么父类错误就会被捕获，而不会捕获到子类错误。因此**将继承结构中子类异常放在较前面的except中捕获，将父类异常放在子类异常后面**
	- 异常继承结构如下

```
BaseException
 +-- SystemExit
 +-- KeyboardInterrupt
 +-- GeneratorExit
 +-- Exception
      +-- StopIteration
      +-- StopAsyncIteration
      +-- ArithmeticError
      |    +-- FloatingPointError
      |    +-- OverflowError
      |    +-- ZeroDivisionError
      +-- AssertionError
      +-- AttributeError
      +-- BufferError
      +-- EOFError
      +-- ImportError
           +-- ModuleNotFoundError
      +-- LookupError
      |    +-- IndexError
      |    +-- KeyError
      +-- MemoryError
      +-- NameError
      |    +-- UnboundLocalError
      +-- OSError
      |    +-- BlockingIOError
      |    +-- ChildProcessError
      |    +-- ConnectionError
      |    |    +-- BrokenPipeError
      |    |    +-- ConnectionAbortedError
      |    |    +-- ConnectionRefusedError
      |    |    +-- ConnectionResetError
      |    +-- FileExistsError
      |    +-- FileNotFoundError
      |    +-- InterruptedError
      |    +-- IsADirectoryError
      |    +-- NotADirectoryError
      |    +-- PermissionError
      |    +-- ProcessLookupError
      |    +-- TimeoutError
      +-- ReferenceError
      +-- RuntimeError
      |    +-- NotImplementedError
      |    +-- RecursionError
      +-- SyntaxError
      |    +-- IndentationError
      |         +-- TabError
      +-- SystemError
      +-- TypeError
      +-- ValueError
      |    +-- UnicodeError
      |         +-- UnicodeDecodeError
      |         +-- UnicodeEncodeError
      |         +-- UnicodeTranslateError
      +-- Warning
           +-- DeprecationWarning
           +-- PendingDeprecationWarning
           +-- RuntimeWarning
           +-- SyntaxWarning
           +-- UserWarning
           +-- FutureWarning
           +-- ImportWarning
           +-- UnicodeWarning
           +-- BytesWarning
           +-- ResourceWarning
```

2. 使用except  Exception捕获异常的时候应将该异常输出，否则很难锁定异常
	- `except Exception as e ; print(e)`

3. finally潜在的两个坑。
	- 在try语句中，finally最终都是会执行的，因此finally语句中的代码可能会对之前代码的结果产生影响 
	- 第一个坑：当finally中存在return或者break的时候，try分支保存的异常在finally语句结束不会被抛出。
	- 第二个坑：当程序运行到try分支中，并且该分支中存在return关键字返回结果的时候，如果此时finally语句中也存在return关键字，那么根据finally必然运行的机制，finally中的返回值会覆盖try其他分支中的返回值