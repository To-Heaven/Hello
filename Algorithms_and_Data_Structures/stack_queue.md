# 栈与队列

## 栈
- 栈是一个后进先出的线性表(last in first out)，**只能在表尾进行删除和插入的操作**

- 栈顶top：栈这个线性表的表尾就是栈顶
- 栈底bottom：栈这个线性表的表头就是栈底

- 空栈：栈中不包含任何元素，栈顶就是栈底。
	- 数据从栈顶进入、栈顶栈底分离，整个栈的当前容量变大，数据出栈时从栈顶弹出，栈顶下移，整个栈的当前容量变小。

- 栈的最大容量：栈占据内存空间的大小
- 栈的当前容量：栈当前存放的元素的个数

#### 栈的特点
1. 栈的元素后进先出
2. 站的操作只能在线性表的表尾进行
3. 本质上是线性表，有两种存储方式
	- 顺序存储结构
	- 链式存储结构 

#### 栈的插入操作push
> 也叫进栈、压栈、入栈


#### 栈的删除操作pop
> 也叫做出站、弹栈

#### 应用
1. 浏览器页面后退回到最近的一次页面
2. 软件的撤销功能

#### 栈的顺序存储结构

```
typedef int ElemType;
typedef struct
{
	ElemType *base;		// 指向栈底的指针变量
	ElemType *top;		// 指向栈顶的指针变量
	int stackSize;		// 栈当前可用的最大容量(空闲空间)
}sqStack;


# define STACK_INIT_SIZE 100
initStack(sqStack *s)
{
	s->base = (ElemType *)malloc(
	STACK_INIT_SIZE * sizeof(ElemType));
	if(!s->base)
		exit(0);
	s->stop = s->base;
	s->stackSize = STACK_INIT_SIZE;
}

```

###### 顺序存储结构的压栈操作
- 在栈顶进行，每次向栈中压入一个数据，top指针就要`+1`，直到栈满为止

```c

```

###### 顺序存储结构的弹栈操作
- 也是在站顶进行，每次从栈中取出一个数据，栈的当前容量就`-1`

```
Pop(sqStack *s, ElemType *e)
{
	if(s->top == s->base)		// 栈空了
		return ;
	*e = *--(s->top)
}
```

###### 顺序存储结构的清空
- 类似高级格式化，只是单纯地清空文件列表而没有覆盖硬盘。
- 只需要下面操作
	- `s->base = s->top`

```c
Clear(sqStack *s)
{
	s->base = s->top
}
```

###### 顺序存储结构的销毁
1. 获取栈的容量
2. 迭代每一个元素，释放该元素并清空 

```

```

###### 计算栈的当前容量

```
int StackLen(sqStack s)
{
	return(s.top - s.base);		// 注意，两个地址如果对应的指针如果是整型的话，它们相减得到的是中间相差元素的个数
}
```


#### 栈的链式存储结构

## 队列