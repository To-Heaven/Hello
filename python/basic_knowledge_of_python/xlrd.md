# 使用 xlrd 操作 excel
- excel文件本质上其实是一个压缩包，excel表格中的数据是由该压缩包中的xml文件保存的，不信你可以试着解压一个`excel`文件


## 基本操作
#### 打开一个excel
- `xlrd.open_workbook(filename=None)`这个方法会返回一个`Book`对象

###### 使用上传下文操作book对象
- `Book`对象内部定义了`__enter__`和`__exit__`方法

```python
    def __enter__(self):
        return self

    def __exit__(self, exc_type, exc_value, exc_tb):
        self.release_resources()
```



#### 操作sheets
- 一个excel文件可以有多个sheet，可以将每一个sheet看作一张表

###### 获取excel文件的sheet所有`sheet对象`组成的列表
- `book.sheets()`

###### 获取excel文件的sheet所有字符串`sheet名称`组成的列表
- `book.sheet_names()`

###### 获取excel的sheet个数
- `book.nsheets`属性


###### 获取单个sheet对象
- 通过`book.sheets()`列表中的索引获取
	- `book.sheet_by_index(num)`

- 通过sheet的name获取
	- `book.sheet_by_name(name)`

#### 操作sheet对象
- 以下都将sheet对象看作一个table

###### 获取表格所在book对象
- `table.book`属性

###### 获取表格名称
- `table.name`

###### 获取表格内的行数和字段数
- `table.nrows`
- `table.ncols`

###### 获取所有单元格数据列表
- `table._cell_values`属性将每一行的内容打包成列表，放在一个大列表中
	
```python
[['客户姓名', 'QQ'], ['吧唧', 12345678], ['第三方', 232255450], ['sdf', 1231287450]]
```

###### 获取指定行/列的指定范围的单元格value
- `col_values(self, colx, start_rowx=0, end_rowx=None)`
	- `table.col_values(1, 1, 5)`表示查看表格第一列中第1行至第4行的数据（不包括第5行）
- `row_values(self, rowx, start_colx=0, end_colx=None)`
	- `table.row_values(2, 3, 5)`表示查看表格第二行中第3列与第4列之间的数据不包括第5列）

###### 获取指定行/列的指定范围的单元格对象
- `col_slice(self, colx, start_rowx=0, end_rowx=None)`
- `row_slice(self, rowx, start_colx=0, end_colx=None)`

###### 获取表格每一行数据
- `table.get_rows()`
	- 返回值为一个存放了`Cell`对象的列表


#### 操作Cell对象
###### 获取单元格存放的值
- `cell.value`


