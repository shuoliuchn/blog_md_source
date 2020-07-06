## 队列

### 队列的 Python 代码实现

队列是一种先进先出的数据类型。

应用场景：我们的计算机实验室有 30 台计算机与一台打印机联网。当学生想要打印时，他们的打印任务与正在等待的所有其他打印任务“一致”。第一个进入的任务是先完成。如果你是最后一个，你必须等待你前面的所有其他任务打印。

接下来，我们将通过 Python 代码创建要给对列：

- `Queue()` 创建一个空的新队列。 它不需要参数，并返回一个空队列。
- `enqueue(item)` 将新项添加到队尾。 它需要 item 作为参数，并不返回任何内容。
- `dequeue()` 从队首移除项。它不需要参数并返回 item。 队列被修改。
- `isEmpty()` 查看队列是否为空。它不需要参数，并返回布尔值。
- `size()` 返回队列中的项数。它不需要参数，并返回一个整数。

```python
class Queue:
    def __init__(self):
        self.items = []
        
    def enqueue(self, item):
        self.items.insert(0, item)
    
    def dequeue(self):
        return self.items.pop()
    
    def isEmpty(self):
        return self.items == []
    
    def size(self):
        return len(self.items)
    
if __name__ == '__main__':
    q = Queue()
    print(q.isEmpty())    # True
    q.enqueue(1)
    q.enqueue(2)
    q.enqueue(3)
    print(q.isEmpty())    # False
    print(q.size())    # 3
    print(q.dequeue())    # 1
    print(q.dequeue())    # 2
    print(q.dequeue())    # 3
```

### 案例：烫手的山芋

烫手山芋游戏介绍：6 个孩子围城一个圈，排列顺序孩子们自己指定。第一个孩子手里有一个烫手的山芋，需要在计时器计时 1 秒后将山芋传递给下一个孩子，依次类推。规则是，在计时器每计时 7 秒时，手里有山芋的孩子退出游戏。该游戏直到剩下一个孩子时结束，最后剩下的孩子获胜。请使用队列实现该游戏策略，排在第几个位置最终会获胜。

分析：

- 在一轮游戏中山芋会被传递 6 次
- 山芋传递的次数不受孩子个数的影响
- 山芋传递 6 次后一轮游戏结束，淘汰一个孩子游戏继续
- 队列：先进先出，只可以从对头取元素，从队尾添加元素
- 准则：保证队头孩子手里面有山芋（谁手里有山芋谁作为队头）
  - 方便删除元素。最终 7 秒到的时候需要将手里有山芋的孩子从队列中剔除。

```python
children = ['A', 'B', 'C', 'D', 'E', 'F']
q = Queue()
# 将6个孩子添加到了队列中
for child in children:
    q.enqueue(child)

while q.size() > 1:
    # 游戏开始，开始传递山芋
    # 山芋传递的次数
    for i in range(6):
        # 让队头的孩子出队列再入队列，成为队尾
        q.enqueue(q.dequeue())
    # 当6次循环结束后，说明山芋被传递了6次，说明一轮游戏结束
    # 一轮游戏结束后，将对头孩子淘汰即可
    q.dequeue()

print('获胜者：',q.dequeue())    # 获胜者： E
```

### 双端队列

同普通队列相比，双端队列有两个头部和尾部。可以在双端进行数据的插入和删除，提供了单数据结构中栈和队列的双重特性。

接下来，我们通过 Python 代码创建一个双端队列

- `Deque()` 创建一个空的新 deque。它不需要参数，并返回空的 deque。
- `addFront(item)` 将一个新项添加到 deque 的首部。它需要 item 参数 并不返回任何内容。
- `addRear(item)` 将一个新项添加到 deque 的尾部。它需要 item 参数并不返回任何内容。
- `removeFront()` 从 deque 中删除首项。它不需要参数并返回 item。deque 被修改。
- `removeRear()` 从 deque 中删除尾项。它不需要参数并返回 item。deque 被修改。
- `isEmpty()` 测试 deque 是否为空。它不需要参数，并返回布尔值。
- `size()` 返回 deque 中的项数。它不需要参数，并返回一个整数。

```python
class Deque:
    def __init__(self):
        self.items = []
        
    def addFront(self, item):
        self.items.append(item)
        
    def addRear(self, item):
        self.items.insert(0, item)
        
    def removeFront(self):
        return self.items.pop()
    
    def removeRear(self):
        return self.items.pop(0)
    
    def isEmpty(self):
        return self.items == []
    
    def size(self):
        return len(self.items)
    
if __name__ == '__main__':
    dq = Deque()
    print(dq.isEmpty())    # True
    dq.addFront(1)
    dq.addRear(2)
    dq.addFront(3)
    dq.addRear(4)
    # 尾 4213 头
    print(dq.isEmpty())    # False
    print(dq.size())    # 4
    print(dq.removeRear())    # 4
    print(dq.removeFront())    # 3
    print(dq.removeFront())    # 1
    print(dq.removeRear())    # 2
```

### 双端队列应用案例：回文检查

回文是一个字符串，读取首尾相同的字符，例如，radar toot madam。

```python
def check_palindrome(s):
    dq = Deque()
    for c in s:
        dq.addFront(c)
    while dq.size() > 1:
        if dq.removeFront() != dq.removeRear():
            return False
    return True

if __name__ == '__main__':
    print(check_palindrome('raddar'))    # True
    print(check_palindrome('上海自来水来自海上'))    # True
    print(check_palindrome('这不是回文'))    # False
    print(check_palindrome(''))    # True
    print(check_palindrome('a'))    # True
    print(check_palindrome('ab'))    # False
```

### 使用两个栈实现队列

利用两个栈实现先进先出的特性，实现一个队列的功能。

```python
class Stack:
    def __init__(self):
        self.items = []    # 构建一个空栈
        
    def push(self, item):    # 从栈顶添加到栈底
        self.items.append(item)
        
    def pop(self):    # 从栈顶向栈底取元素
        return self.items.pop()
    
    def peek(self):    # 返回栈顶元素下标
        return len(self.items) - 1
    
    def isEmpty(self):
        return self.items == []
    
    def size(self):
        return len(self.items)

class Queue:
    def __init__(self):
        self.s1 = Stack()
        self.s2 = Stack()
        
    def enqueue(self, item):
        while self.s1.size():
            self.s2.push(self.s1.pop())
        self.s2.push(item)
        while self.s2.size():
            self.s1.push(self.s2.pop())
    
    def dequeue(self):
        return self.s1.pop()
    
    def isEmpty(self):
        return self.s1.isEmpty()
    
    def size(self):
        return self.s1.size()
    
if __name__ == '__main__':
    q = Queue()
    print(q.isEmpty())    # True
    q.enqueue(1)
    q.enqueue(2)
    q.enqueue(3)
    print(q.isEmpty())    # False
    print(q.size())    # 3
    print(q.dequeue())    # 1
    print(q.dequeue())    # 2
    print(q.dequeue())    # 3
```

