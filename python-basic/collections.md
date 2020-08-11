## collections 模块

[TOC]

### 统计

现在有一个列表，我们需要统计每个元素出现的个数，可以通过循环来实现：

```python
lst = [11, 2, 2, 123, 1, 1, 123, 12, 12, 32, 12, 31, 23, 123, 21, 3]
dic = {}
for i in lst:
    dic[i] = lst.count(i)
print(dic)
```

输出的结果为：

```python
{11: 1, 2: 2, 123: 3, 1: 2, 12: 3, 32: 1, 31: 1, 23: 1, 21: 1, 3: 1}
```

如果使用 collections 模块的统计功能，将会更便捷地获得我们想要的结果：

```python
from collections import Counter
lst = [11, 2, 2, 123, 1, 1, 123, 12, 12, 32, 12, 31, 23, 123, 21, 3]
print(Counter(lst))    # 获取的是 Counter 字典，可以通过 dict 方法转换为普通字典
```

输出的结果为：

```python
Counter({123: 3, 12: 3, 2: 2, 1: 2, 11: 1, 32: 1, 31: 1, 23: 1, 21: 1, 3: 1})
```

### 有序字典

Python 3 中的字典已经是有序的了，但是对于 Python 2 来说，还是无序的。有序字典在 Python 2 中还是十分有用的：

```python
from collections import OrderedDict
a = OrderedDict({'key1': 1, 'key2': 2})
print(a)
print(a['key1'])
```

输出的结果为：

```python
OrderedDict([('key1', 1), ('key2', 2)])
1
```

可以看出，有序字典以一个类似于列表的形式存储。

### 默认字典

使用默认字典可以快速对字典进行操作：

```python
from collections import defaultdict
dic = defaultdict(list)
dic['key1'].append(10)    # 默认创建键值对'key1':[]，可以使用append方法增加元素
dic['key2'] = 123    # 将默认值替换
dic['key3']    # 默认创建键值对'key3':[]
print(dic)
```

输出的结果为：

```python
defaultdict(<class 'list'>, {'key1': [10], 'key2': 123, 'key3': []})
```

我们从前有一道作业题，把列表中小于 66 的元素放到字典键 `'key1'` 对应的值的列表中，大于 66 的元素放到字典键 `'key2'` 对应的值的列表中，从前我们是这样做到的：

```python
lst = [11, 22, 33, 44, 55, 77, 88, 99]
dic = {'key1': [], 'key2': []}
for i in lst:
    if i > 66:
        dic['key2'].append(i)
    else:
        dic['key1'].append(i)
print(dic)
```

更巧妙一点的写法为：

```python
lst = [11, 22, 33, 44, 55, 77, 88, 99]
dic = {}
for i in lst:
    dic.setdefault('key2', []).append(i) if i > 66 else dic.setdefault('key1', []).append(i)
print(dic)
```

使用默认字典，则可以这样写：

```python
from collections import defaultdict
lst = [11, 22, 33, 44, 55, 77, 88, 99]
dic = defaultdict(list)
for i in lst:
    if i > 66:
        dic['key2'].append(i)
    else:
        dic['key1'].append(i)
print(dic)
```

### 双端队列

队列是一种先进先出的数据存储方式，栈则是先进后出的数据存储方式。

Python 中的列表是一种栈：

```python
lst = []
lst.append(1)
lst.append(2)
lst.append(3)
lst.append(4)
lst.append(5)
print(lst)
lst.pop()
print(lst)
lst.pop()
print(lst)
lst.pop()
print(lst)
lst.pop()
print(lst)
lst.pop()
print(lst)
```

也可以通过在 pop 指定删除第一位的方法模拟成为队列：

```python
lst = []
lst.append(1)
lst.append(2)
lst.append(3)
lst.append(4)
lst.append(5)
print(lst)
lst.pop(0)
print(lst)
lst.pop(0)
print(lst)
lst.pop(0)
print(lst)
lst.pop(0)
print(lst)
lst.pop(0)
print(lst)
```

双端队列则是可以实现在数据两端灵活删除数据的数据结构：

```python
from collections import deque
lst = deque([11, 22, 33, 44])
print(lst)
lst.append(55)    # 在右侧（后方）插入数据
print(lst)
lst.appendleft(66)    # 从左侧（前方）插入数据
print(lst)
lst.pop()    # 删除后输入的数据（栈）
print(lst)
lst.popleft()    # 删除先输入的数据（队列）
print(lst)
lst.insert(2, 77)    # 指定位置插入
print(lst)
```

输出的结果为：

```python
deque([11, 22, 33, 44])
deque([11, 22, 33, 44, 55])
deque([66, 11, 22, 33, 44, 55])
deque([66, 11, 22, 33, 44])
deque([11, 22, 33, 44])
deque([11, 22, 77, 33, 44])
```

编程中主要的数据存储结构有：

```
列表，队列，双端队列，单向链表，双向链表
```

Python 中的垃圾回收机制称为 GC

以引用计数为主，标记清除和分带回收为辅

### 命名元组

命名元组用来使用关键字快速找到相应的数据：

```python
from collections import namedtuple
dg = namedtuple('dg', ['jd', 'wd', 'gd'])
nt = dg(116, 40, 8848)
print(nt)
print(nt.jd)
```

输出的结果为：

```python
dg(jd=116, wd=40, gd=8848)
116
```

我们从前学过的 time 模块的结构化时间就是一种命名元组。