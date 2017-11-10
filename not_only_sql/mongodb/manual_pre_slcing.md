## 手动预先分片

#### sh.splitAt(fullName, middle)
- 需要提前使用个此函数告诉mongos
- fillName: 要被切分的对象
- middle: 字段名（被切分对象的shard key），用来指定分片条件
- 比如
	- `sh.splitAt("shop.user", {userid: i*1000})`
	- 表示对shop下的user表进行切片