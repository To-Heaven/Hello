# PostgreSQL约束

## CONSTRAINT
- 使用`constraint`关键字可以为我们创建的约束指定名称，如果我们没有使用该关键字，PostgreSQL会默认帮我们生成一个名称
- 格式
	- `CONSTRAINT 名称 + 约束`

## 语法
- 约束需要定义在数据类型之后，default设置默认值时也是在数据类型之后，但是约束与default之间可以按任意顺序排列



#### 字段约束与表约束
- 字段约束中，CHECK表达式是在字段类型之后的
- 表约束中，CHECK表达式与字段定义分开写
- 注意
	- **字段约束可以写成表约束，但是表约束不一定能写成字段约束（比如联合主键）**
```
ziawang=# create table goods(
ziawang(# id int primary key ,
ziawang(# name char(20),
ziawang(# price int,
ziawang(# check (price > 0),
ziawang(# discount_price int ,
ziawang(# check (discount_price > 0 and price > discount_price)
ziawang(# );
CREATE TABLE
ziawang=# 

```



## CHECK 检测约束
- 检测约束一般用来对字段的值进行检测和约束，比如要求某个数值类型字段的值大于一个数，或者大于另一个数值字段的值
- 比如，在产品表中，要求产品折扣前的价格要低于产品的定价，并且要求产品定价为正数


#### CHECK 表达式结构
- `CHECK (expression)`
	- expression中要包含受约束的字段

#### expression
- 由上面例子可以知道在表达式中是可以使用逻辑表达式的。
	- `<>`不等于
	- `>`大于
	- `<`小于
- 注意
	- **当expression表达式的计算结果为NULL的时候，check 约束会认为是满足条件的**