# 循环链表

#### 单链表
- 每一个节点只存储向后的指针，只能索引houji节点，不能索引前驱节点，如果我们不从单链表的头结点触发的话，是无法访问全部节点的。

#### 循环链表
- 解决单链表的上述问题的一种方法就是循环链表。
- 我们如果把单链表终端节点的指针由空指针NULL改为指向头结点的指针，这样就形成了一个循环链表（单循环链表）
- 空循环链表
	- 最后一个指针与head指针指向同一个节点对象
		- 即"head->next = head"(head是指向第一个节点的指针)

#### 循环链表的特点
- 不需要增加链表的存储量，仅仅对连接方式稍作改变就可以使得链表处理更加方便灵活

#### 举例：将两个链表连接成一个线性表的运算
- 将(a1, a2, ..., an) 与 (b1, b2, ..., bn)连接成一个线性表(a1, a2, ..an, b1, b2, ..., bn)

- 如果使用单链表或者**头指针表示的单循环链表**上做这种链接操作，就需要遍历第一个链表，找打节点an，然后将节点b1连接到an后面，时间复杂度为O(n)

- 使用尾指针表示的单循环连表的话，只需要修改指针，不需要遍历，即将指向an的尾指针指向b1， 释放掉指向b1的头指针，将指向bn的尾指针指向a1，执行的时间复杂度为O(1)

```
LinkList Connect(LinkList A, LinkList B)
{
	Link p = A->next;		// 保存A链表的头结点位置
	A->next = B->next-next;	// B表开始节点链接到A表为
	free(B->next);		// 释放B表头结点
	B->next=p;
	return B;		// 返回新循环链表的尾指针
}
```

#### 判断循环列表是否为空
- 循环链表中使用尾指针rear来方位循环链表的其他节点，并没有使用头指针
- `rear = rear->next`


#### 判断单链表中是否有环
- 有环： 链表的尾结点指向了链表中更的某一个节点

###### 方法一
- 使用p, q两个指针，p总是一个节点一个节点向前走，但是q每次都要从头开始走，并且保证两者每次所在的节点都是相同的，如果某一次走完的时候，q走的步数与p不同，那么就出现了环

###### 方法二
- 使用p，q快慢指针，p每次向前走一步，q每次走两部，如果在某一个时候p=q，那么就存在环




#### 注意
- 循环链表不一定必须要有头结点

#### 查询
- 循环链表中，终端节点使用尾指针rear指示，那么查找终端节点的时间复杂度就是O(1)，开始节点也是O(1)
	- 头结点 = rear->next->next


