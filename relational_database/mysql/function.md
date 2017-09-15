# MySQL函数

## 计算日期
- `curdate()`
	- 当前日期`year-month-day`
- `timestampdiff(日期单位, time1, time2)`
	- *parameters*
		- 日期单位：year, month等
		- time1：起始时间
		- time2：结束时间

```sql
mysql> SELECT name, birth, CURDATE(),
    -> TIMESTAMPDIFF(YEAR,birth,CURDATE()) AS age
    -> FROM tb;

```

- month(date)
	- 返回date中的月份值

- date_add(date    interval expr   unit)
	- 对指定date以unit为单位进行增加或减少
		- date：指定日期
		- interval expr：为正数的时候表示增加unit对应日期单位的值，反之减少
		- unit：日期单位，可以是year、month等

```sql
mysql> SELECT name, birth FROM pet
    -> WHERE MONTH(birth) = MONTH(DATE_ADD(CURDATE(),INTERVAL 1 MONTH));

```


## 统计
- count()
	- `count(*)` 可以用来统计记录的数目
	 


