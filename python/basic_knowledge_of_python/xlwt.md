# 使用xlwt创建excel文件

#### 创建一个excel文件的步骤
1. 实例化一个`book`对象
2. 为`book`对象创建一个`sheet`（表格）
3. 向`sheet`中插入数据
4. 保存为文件


###### 1. 实例化`book`对象
- `book = Workbook(encoding="ascii")`
	- 可以使用encoding指定编码，建议使用`utf-8`

- 源码（部分）如下

```python
class Workbook(object):
    """
    This is a class representing a workbook and all its contents. When creating
    Excel files with xlwt, you will normally start by instantiating an
    object of this class.
    """

    def __init__(self, encoding='ascii', style_compression=0):
        self.encoding = encoding
```


###### 2. 创建一个`sheet`
- `sh = book.add_sheet(self, sheetname, cell_overwrite_ok=False)`

- 该方法返回的是一个`Worksheet`对象，源码如下（部分）

```python
class Worksheet(object):
    """
    This is a class
    representing the contents of a sheet in a workbook.

    .. warning::

      You don't normally create instances of this class yourself.					# 告诉我们不要通过实例化来创建sheet对象，这里只是为了了解他的相关属性
      They are returned from calls to :meth:`~xlwt.Workbook.Workbook.add_sheet`.
    """
    # a safe default value, 3 is always valid!
    active_pane = 3					# 这是什么鬼？
    
    def __init__(self, sheetname, parent_book, cell_overwrite_ok=False):
        self.Row = Row
        self.Column = Column

        self.__name = sheetname
        self.__parent = parent_book
```

- 贴出源码并不是为了让你通过实例化的方式创建一个`sheet`对象，而是看一下`sheet`有什么属性可以拿来用，比如上面我们就可以使用
	- `sh.Row`
	- `sh.Column`
	- 下面两个属性不到玩不得以我们最好还是别使用，毕竟人家都设置成私有属性了 = = ！ 
		- `sh._worksheet__parent`
		- `sh._worksheet__name`


###### 3. 插入数据
- `sh.write(row_num, col_num, value)`
	- `row_num`要写入值的单元格所在的行，从`0`开始！
	- `col_num`要写入值的单元格所在的列，也是从`0`开始
	- `value`要写入的值


###### 4. 保存成文件
- `book.save(filename)`
	- `filename`是要保存的文件的名称，可以是`.xls`后缀也可是`xlsx`后缀

- **如果不使用此方法保存文件，那么文件将不会被创建**

#### 实例

```python
import xlwt 

book = xlwt.Workbook(encoding='utf-8')

work_sheet = book.add_sheet(sheetname='customer')


work_sheet.write(0, 0, 'hello')
work_sheet.write(0, 1, 'ziawang')
work_sheet.write(1, 0, '你好')
work_sheet.write(1, 1, '王子豪')

book.save(filename_or_stream='ziawang.xlsx')
```
