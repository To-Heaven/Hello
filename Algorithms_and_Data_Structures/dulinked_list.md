# 双向链表

#### 特点
- 可以有效的提高算法的时间性能，其实就是以空间换时间的方式

#### 双向链表的结点结构

```c
typedef struct DualNode
{
	ElemType data;
	struct DualNode *prior;    // 前驱结点
	struct DualNode *next;	   // 后继结点
}DualNode, *DuLinkList;
```

- 双向链表中的单个结点除了一个数据域存放数据元素之外，还要有两个指针域分别指向该节点的前驱结点和后继结点

#### 双向链表的插入操作
- 现在有q，p两个双向链表的结点，要把结点s插入q与p中间，需要一次执行以下四个步骤

```c
s->next = p;
s->prior = p->prior;
p->prior->next = s;
p->prior = s
```

#### 双向链表的删除操作

```c
s->prior-next = s->next;
s->next->prior = s->prior;
free(s)
```

## 双循环链表
- 有双向链表结点组成的循环链表就是双循环链表

#### 双向循环链表实践
