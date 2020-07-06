## 栈

栈是一种先进后出的数据结构。

栈顶，栈底

- 从栈顶向栈底添加元素，从栈顶取元素

应用：每个 web 浏览器都有一个返回按钮。当你浏览网页时，这些网页被放置在一个栈中（实际是网页的网址）。我们正在查看的网页在顶部，第一个查看的网页在底部。如果按 `返回` 按钮，将按相反的顺序浏览之前的页面。

接下来，我们通过 Python 代码实现一个栈：

- `Stack()` 创建一个空的新栈。 它不需要参数，并返回一个空栈。
- `push(item)` 将一个新项添加到栈的顶部。它需要 item 做参数并不返回任何内容。
- `pop()` 从栈中删除顶部项。它不需要参数并返回 item。栈被修改。
- `peek()` 从栈返回顶部项，但不会删除它。不需要参数。 不修改栈。
- `isEmpty()` 测试栈是否为空。不需要参数，并返回布尔值。
- `size()` 返回栈中的 item 数量。不需要参数，并返回一个整数。

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
    
if __name__ == '__main__':
    s = Stack()
    print(s.isEmpty())    # True
    s.push(1)
    s.push(2)
    s.push(3)
    print(s.isEmpty())    # False
    print(s.peek())    # 2
    print(s.size())    # 3
    print(s.pop())    # 3
    print(s.pop())    # 2
    print(s.pop())    # 1
```

