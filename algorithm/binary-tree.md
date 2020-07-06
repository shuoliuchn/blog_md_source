







## 二叉树

[TOC]

### 二叉树的概念和实现

- 根节点：树中上部的节点

- 左叶子节点

- 右叶子节点

- 子树

  - 完整的子树

    - 一个根节点，左右叶子节点组成

  - 不完整的子树

    - 根节点，左叶子节点

    - 根节点，右叶子节点

    - 根节点

      - 特点：每一个节点都可以作为某一颗子树的根节点

![二叉树](binary-tree.assets/%E4%BA%8C%E5%8F%89%E6%A0%91.png)

使用 Python 实现一个二叉树：

```python
class Node:
    def __init__(self, item):
        self.item = item
        self.left = None    # 指向该节点的左叶子节点
        self.right = None    # 指向该节点的右叶子节点
        
class Tree:
    def __init__(self):    # 构建一颗空树
        self.root = None    # 永远指向二叉树中的根节点
    # 自上到下从左到右的准则插入新的节点
    def insert(self, item):    # 向树种插入一个节点
        node = Node(item)
        # 如果树为空
        if self.root == None:
            self.root = node
            return
        # 如果树为非空
        cur = self.root
        q = [cur]
        while True:
            n = q.pop(0)
            if n.left == None:
                n.left = node
                break
            else:
                q.append(n.left)
            if n.right == None:
                n.right = node
                break
            else:
                q.append(n.right)
                
    def travel(self):
        q = [self.root]
        while q:
            n = q.pop(0)
            print(n.item)
            if n.left != None:
                q.append(n.left)
            if n.right != None:
                q.append(n.right)
                
tree = Tree()
tree.insert(1)
tree.insert(2)
tree.insert(3)
tree.insert(4)
tree.insert(5)
tree.travel()    # 1 2 3 4 5
```

### 二叉树的遍历

二叉树有两种常用遍历形式：

- 广度遍历
  - 上述代码中的遍历，就是广度遍历。自上到下逐行遍历叫做广度遍历。
  - 横向遍历
  - 使用队列实现
- 深度遍历：纵向遍历。前中后序遍历形式需要作用到子树中。前中后序中的前中后指的是子树中根节点的位置
  - 前序：根左右，先遍历子树的根节点，再遍历子树的左节点然后是右节点
  - 中序：左根右
  - 后序：左右根
  - 使用递归实现

广度遍历我们已经实现，接下来就实现三个深度遍历。

深度遍历的实现思路：

- 深度遍历是需要作用到每一颗子树中
- 子树和子树之间的区别体现在根节点中。
- 如果写一个函数，该函数可以将一个子树中的节点进行遍历，则将该函数作用到其他子树中就可以将整棵树进行深度遍历。

```python
class Node:
    def __init__(self, item):
        self.item = item
        self.left = None
        self.right = None
        
class Tree:
    def __init__(self):
        self.root = None
        
    def insert(self, item):
        node = Node(item)
        if self.root == None:
            self.root = node
            return
        q = [self.root]
        while True:
            n = q.pop(0)
            if n.left == None:
                n.left = node
                break
            else:
                q.append(n.left)
            if n.right == None:
                n.right = node
                break
            else:
                q.append(n.right)
    # 前序遍历
    # 参数root表示的是子树的根节点，需要给递归调用的forward传入不同子树的根节点
    def forward(self, root):    # 根左右
        if root == None:
            return
        print(root.item)
        self.forward(root.left)
        self.forward(root.right)
    # 中序遍历
    def middle(self, root):    # 左根右
        if root == None:
            return
        self.middle(root.left)
        print(root.item)
        self.middle(root.right)
    # 后序遍历
    def back(self, root):    # 左右根
        if root == None:
            return
        self.back(root.left)
        self.back(root.right)
        print(root.item)
        
tree = Tree()
tree.insert(1)
tree.insert(2)
tree.insert(3)
tree.insert(4)
tree.insert(5)
tree.insert(6)
tree.insert(7)
tree.forward(tree.root)    # 1 2 4 5 3 6 7
tree.middle(tree.root)    # 4 2 5 1 6 3 7
tree.back(tree.root)    # 4 5 2 6 7 3 1
```

### 排序二叉树

二叉树的优势在于查询速度快。但是查询速度快是要建立在有序的基础上的。对于无序二叉树，还是需要遍历才能找到数据。给二叉树排序，创建有序二叉树，是一个很重要的事情。

接下来，我们就实现要给排序二叉树。

```python
class Node:
    def __init__(self, item):
        self.item = item
        self.left = None
        self.right = None
        
class SortTree:
    def __init__(self):
        self.root = None
        
    def add(self, item):    # 将节点插入到排序二叉树中
        node = Node(item)
        if self.root == None:    # 树为空的情况
            self.root = node
            return
        # 树为非空
        cur = self.root
        while True:
            if cur.item > item:    # 插入的节点值小于根节点，该节点需要插入到根节点的左侧
                if cur.left == None:    # 如果左节点为空，则直接插入
                    cur.left = node
                    break
                cur = cur.left    # 左节点为非空，继续查找
            else:    # 插入节点的值大等于于根节点，该节点需要插入到根节点的右侧
                if cur.right == None:
                    cur.right = node
                    break
                cur = cur.right
    # 中序遍历
    def middle(self, root):
        if root == None:
            return
        self.middle(root.left)
        print(root.item)
        self.middle(root.right)
        
stree = SortTree()
lst = [3, 5, 2, 4, 6, 1, 7]
for i in lst:
    stree.add(i)
stree.middle(stree.root)    # 1 2 3 4 5 6 7
```

