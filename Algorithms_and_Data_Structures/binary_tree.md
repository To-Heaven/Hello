# 二叉树


#### 二叉树是什么鬼？
- 二叉树是是每个节点最多只有两个分支的树结构。通常分支被称作“左子树”和“右子树”。二叉树的分支具有左右次序，不能颠倒。

 
#### 使用python定义一个二叉树的节点
```python
class BinaryTreeNode(object):
    """ 二叉树节点类

    """

    def __init__(self, data):
        self.data = data
        self.lchild = None
        self.rchild = None
```


## 二叉树的遍历
- 二叉树的遍历其实就是按照一定的会这和顺序遍历二叉树的所有节点，**保证每一个二叉树的节点都会被访问一次且只能被访问一次**
- 由于二叉树是非线性结构，所以对于二叉树的遍历本质上就是将二叉树的每一个几点转换成一个线性序列来表示

#### 前序遍历的python实现

```python
class BinaryTreeNode(object):
    """ 二叉树节点类

    """

    def __init__(self, data):
        self.data = data
        self.lchild = None
        self.rchild = None

a = BinaryTreeNode('A')
b = BinaryTreeNode('B')
c = BinaryTreeNode('C')
d = BinaryTreeNode('D')
e = BinaryTreeNode('E')
f = BinaryTreeNode('F')
g = BinaryTreeNode('G')

e.lchild = a
e.rchild = g
a.rchild = c
c.lchild = b
c.rchild = d
g.rchild = f

root = e

def pre_order(root):
    """ 前序遍历
    Args:
        root: 根节点对象
    """

    if root:
        print(root.data, end='')
        pre_order(root.lchild)
        pre_order(root.rchild)
```

#### 中序遍历的python实现


```python
def middle_order(root):
    """ 中序遍历
    Args:
        root: 根节点对象
    """

    if root:
        middle_order(root.lchild)
        print(root.data, end='')
        middle_order(root.rchild)


```

#### 后序遍历的python实现

```python
def after_order(root):
    """ 后序遍历
    Args:
        root: 根节点对象
    """

    if root:
        after_order(root.lchild)
        after_order(root.rchild)
        print(root.data, end='')
```

#### 层次遍历的python实现

```python
from collections import deque

def level_order(root):
    ''' 层次遍历，每一次将队列中的节点取出，然后将其左子节点和右子节点一次放进队列
    Args:
        root: 根节点对象
    '''

    queue = deque()
    queue.append(root)
    while len(queue) > 0:
        node = queue.popleft()
        print(node.data,end='')
        if node.lchild:
            queue.append(node.lchild)
        if node.rchild:
            queue.append(node.rchild)

level_order(root)
```

## 二叉搜索树 bst

#### 二叉搜索树的特点
1. 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值
2. 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值
3. 任意节点的左、右子树也分别为二叉查找树
4. 没有键值相等的节点

#### 复杂度
- 二叉搜索树的`搜索、插入、删除`的复杂度等于树的高度
- 最好的情况，时间复杂度为`O(logn)`
- 最差的情况，时间复杂度为`O(n)`，这种情况下二叉树退化成了线性表
	- 改进版的二叉搜索树，比如`AVL自平衡查找树`

AVL自平衡查找树
- 特点：AVL树中任何节点的两个字数的高度之差最大为1
- 时间复杂度：查找、插入、删除再平均和最坏情况下都是`logn`
	- 增加和删除可能需要通过一次或多次的旋转来重新平衡这树




## B-Tree树
介绍
- Btree 是一个自平衡的多路搜索树。


应用
- 用于构建数据库索引



