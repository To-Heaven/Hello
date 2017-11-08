## 线性表的顺序存储结构
- 用一段地址连续的存储单元依次存放线性表中的元素
	- 物理上： 在内存中找个存储地址，通过占位的方式，将内存中一定的内存空间占据了，然后具有相同类型的元素一次插入到该内存空间中。

#### 线性表与数组
- 线性表是一种抽象数据结构，在算法中，我们认为线性表的起始位置是从1开始的。而数组则是从0开始的

## 结构代码

```c
#define MAXSIE 20
typedef int ElemType;
typedef struct{
	ElemType data[MAXSIZE]；
	int length;
}Sqlist;
```

#### 顺序存储结构封装的三个属性
###### 存储空间的起始位置

###### 线性表的最大存储容量MAXSIZE
- 数组的长度

###### 线性表的当前长度length
- 线性表的当前长度


## 地址计算方法
- 假设： 每一个ElemType战争用c个存储单元(字节)
- 线性表中第i+1元素与第i元素之间的存储位置关系(LOC时获取u存储位置的函数)
	- LOC(ai+1) = LOC(ai) + c
- 结论：
	-  对于第i个元素的存储位置 `LOC(ai) = LOC(a1) + (i-1)*c`
- 推论： 我们可以随时计算尺线性表中任意位置的存储地址，不管该元素在线性表的开始还是结束，他的**存储时间性能**都是O(1)，这种结构通常称为随机存储结构

## 线性表顺序存储结构的方法C语言实现
#### GetElem(SqList L int i, ElemType *e)

```c
#define OK 1
#define ERROR 0
#define TRUE 1
#define False 0


typedef int Statue;

// Status 是函数的类型，它的值是函数结果状态代码，如OK；
Status GetElem(SqlList L, int i, ElemType *e){
	if (L.length==0 || i<1 || i>L.length ){
		return ERROR;
	} 
	
	*e = data[i-1];
	
	return OK;
} 
```

#### ListInsert(Sqlist *L, int i, ElemType e)
###### 插入算法思路
1. 如果插入的位置不合理，抛出异常
2. 如果线性表的长度大于的数组的长度，抛出异常或者东爱增加数组容量
3. **从最后一个元素开始向前遍历到第i个位置**，分别将它们向后挪动一个位置
4. 将要插入的元素填入位置i处
5. 线性表长度加1

```c
Status ListInsert(SqList *L, int i, ElemType e){
	int k;
	
	if (L->length == MAXSIZE){
		return ERROR;
	}
	
	if (i > L->length || i < 1 ){
		return ERROR;
	}
	
	if (i<= L->length){
		for (k=L->length-1; k>=i-1; k--){
			L->data[k+1] = l->data[k];
		}
	}
	
	l->data[i-1] = e;
	l->length++;
	
	return OK;
}
```


#### ListDelete(sqList *L, int i, ElemType e)
###### 删除算法思路
1. 如果删除的位置不合理，抛出错误
2. 取出删除元素
3. 从删除元素位置开始遍历到最后一个元素，分别将它们都向前移动一个位置
4. 表长减1

```c
Status ListDelete(SqList *L, int i, ElementType *e) 
{
	int k;
	
	if(L->length == 0)
	{
		return ERROR;
	}
	
	if (i<1 || i>L->length)
	{
		return ERROR;
	}
	
	*e = L->data[i-1];
	
	if (i<L->length)
	{
		for(k=1; k<L->length; k++)
		{
			for (k=il k<L->length; k++)
			{
				L-data[k-1] = L->data[k];
			}
		}
	}
	L->length--;
	
	return OK;
}
```

## 时间复杂度
#### 存、读数据
- 不管在哪一个位置，事件复杂度都是O(1)

#### 插入、删除数据
- 对于插入和删除来说。
	- 数据刚好在最后一个位置，就不需要移动任何线性表中的任何元素，此时事件复杂度为O(1)
	- 数据刚好在第一个位置，意味着所有的元素都要移动一次，因此事件复杂度为O(n)
- 综合来看，线性表的插入和删除操作的事件复杂度为O(n)
	- O((n-1)/2)

## 应用
- 线性表的顺序存储结构适合元素个数比较稳定，不经常插入和删除元素，更多操作时存取元素的应用

## 顺序存储结构的优缺点
#### 优点
- 无需为表中元素之间的逻辑关系而增加额外的存储空间
- 快速存取表中任意元素

#### 缺点
- 插入与删除经常需要移动大量元素
	- 原因在于元素之间的关系
- 长度变化较大时，难以确定存储空间容量
- 容易造成存储空间碎片



