## 线性表的链式存储结构

#### 链式存储结构是什么鬼
- 链式存储结构用一组任意的存储单元存储线性表的数据元素，这组存储单元可以在内存中未被占用的任意位置，每一个数据元素除了存储其本身数据元素信息之外，还要存储指向其后继存储元素的信息。
	- 数据域：存储数据信息的域
	- 指针域：存储直接后继位置
	- 节点Node = 数据域 + 指针域

## 单链表
- 链表中的每一个节点中只包含一个指针域

#### 特点
- 第一个节点的存储位置是头指针，头指针head指向连表中第一个节点（头结点）
- 最后一个节点指针为NULL

#### 头指针与头节点
###### 头结点
- 数据域一般不存储数据。
- 头结点为了操作同一和方便而设立，放在第一个元素的节点之前，其数据与一般没有意义，不过也可以用来存放链表的长度
- 有了头结点，对在第一个元素节点（头结点后的第一个节点）前插入和删除节点的操作就与其他节点的操作及统一了
- 头结点不是链表的必要元素

```
头指针 --> 头结点 ---> a1(数据域) | p(指针域)

```

###### 头指针
- 指向链表第一个节点的指针，如果链表有头节点，则是指向头结点的指针
- 头指针具有标识最用，常用头指针指针变量的名字作为链表的名字
- 无论链表是否为空，头指针均为空，因为一旦头指针不存在，那么链表也就不存在了。
- 因此，头指针是链表的必要元素


#### 使用结构指针表述单链表

```c
# 节点由存放数据元素的数据域和存放后继节点位置的指针域组成 

typedef struct Node
{
	ElemType data;	# 数据域
	struct Node* Next;	# 指针域		
}Node;
typedef struct Node* LinkList;     	# 取别名
```

```
# 

int *i, j	# i是指针，j不是指针	

*int i, j	# i和j都是指针
```

###### 描述举例
- p 为一个指向线性表第i个元素ai的指针
	- p->data  : ai的数据域
	- p->next  : ai的指针域
		- p->next指针域中指针指向的是ai+1元素节点的位置
		- p->next->data指第ai+1元素节点的数据域


#### 单链表的读取
###### 特点
- 必须从第一个元素开始遍历查找

###### 注意
- 单链表中没有定义表长，因此要遍历单链表时候，我们不知道要循环多少次，所以不能使用for迭代来遍历单链表，应该使用while循环

###### 获取第i个元素算法思路
1. 生命一个系欸但p指向链表的第一个结点，初始化j从1开始
2. j<i的时候，遍历列表，让p的指针向后移动，不断只想下一个结点，并且j+1
3. 当遍历到链表末尾时，p为空，说明第i个元素不存在，否则就查找成功，返回节点p的数据

```c
Status GetElem(LinkList L; int i , ElemType *e)
{
	int j;
	LinkList p;
	
	p = L->Next;
	j = 1;
	
	while (p && j<i)
	{
		p = p->next;
		++j;
	}
	
	if (!p || j>1)
	{
		return ERROR;
	}
	
	*e = p->data;
	
	return OK;
}
```

- 查找操作时间复杂度
	- 单链表查找第i个元素的时间复杂度取决于第i个元素的位置，如果该元素刚好在第一个，那么时间复杂度为O(1)，如果刚好在最后一个，时间复杂度就是O(n)
	- 因此综合来看，但单链表的获取元素的时间复杂度为O(n)




###### "工作指针后移"