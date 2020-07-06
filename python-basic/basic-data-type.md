## 基础数据类型补充与总结

[TOC]

### 整型

`.bit_length` 方法用来计算整型数字转换为二进制所占的位数：

```python
a = 10
print(a.bit_length())

输出的结果为： 4
```

这个方法的意思是，10 转换为二进制数是 1010，一共会占 4 位。

补充一个 Python 中表示无穷大的方法：

```python
a = float('inf')    # 正无穷大，inf 也可以写作 INF
b = float('-inf')    # 负无穷大，inf 也可以写作 INF
print(a > 10000000000000000000000000000)    # True
print(b > -10000000000000000000000000000)    # False
```

### 字符串

#### `.capitalize()` 方法

`.capitalize()` 方法用来将字符串的首字母大写：

```python
s = 'alEX wusir'
s1 = s.capitalize()
print(s1)

输出的结果为：Alex wusir
```

#### `.title()` 方法

`.title()` 方法能将字符串每个单词的首字母大写，区分首字母的方式有空格、符号和数字等：

```python
s = 'alEX wUsir-meET3san'
s1 = s.title()
print(s1)

输出的结果为：Alex Wusir-Meet3San
```

#### `.index()` 和 `.find()` 方法

`.index()` 方法用来通过元素查找索引，查找不到时会报错：

```python
s = 'alex wusir'
print(s.index('e'))
print(s.index('wu'))
```

`.find()` 方法同样用来通过元素查找索引，与 `.index()` 方法不同的是，`.find()` 方法查找不到时会返回 -1，而不会报错：

```python
s = 'alex wusir'
print(s.find('b'))
print(s.find('le'))
```

如果字符串中有多个要查找的元素片段，`.index()` 和 `.find()` 方法都只会找第一个元素的索引：

```python
a = 'abcabc'
print(a.index('a'))
print(a.find('a'))
```

#### `.center()` 方法

`.center()` 方法用来进行居中操作，会在字符串两端插入等量的空格，使得字符串总长度等于输入的参数：

```python
s = 'alex wusir'
print(s.center(20))
```

也可以指定两端填充的内容：

```python
print(s.center(20, '-'))

输出的结果为：-----alex wusir-----
```

#### `.ljust()` 和 `.rjust()` 方法

这两个方法与 center 方法类似，用于填充指定的字符。ljust 方法会在字符串的右侧填充字符，rjust 会在字符串左侧填充字符。默认填充空格，也可以指定填充字符的内容：

```python
>>> a = 'abc'
>>> a.rjust(5)
'  abc'
>>> a.ljust(5)
'abc  '
>>> a.rjust(5, '-')
'--abc'
>>> a.ljust(5, '-')
'abc--'
>>> a.rjust(2)
'abc'
```

#### `.format()` 方法

`.format()` 方法是另外一种字符串格式化的方法：

```python
s = 'alex{}wu{}si{}r'
s1 = s.format('你好', '我好', '大家好')
print(s1)

输出的结果为：alex你好wu我好si大家好r
```

`.format()` 方法除了向上面这种按照位置格式化之外，还可以按照索引格式化：

```python
s = 'alex{0}wu{2}si{1}r'
s1 = s.format('你好', '我好', '大家好')
print(s1)

输出的结果为：alex你好wu大家好si我好r
```

`.format()` 方法还可以按照关键字进行格式化：

```python
s = 'alex{a}wu{c}si{b}r'
s1 = s.format(b = '你好', a = '我好', c = '大家好')
print(s1)

输出的结果为：alex我好wu大家好si你好r
```

#### `.swapcase()` 方法

`.swapcase()` 方法可以反转字符串中字母的大小写：

```python
s = 'alEx'
s1 = s.swapcase()
print(s1)

输出的结果为：ALeX
```

这里插入一个 PyCharm 的使用技巧：`ctrl + shift + r` 打开替换窗口。

### 列表

#### `.reverse()` 方法

对列表进行反转操作，我们可以通过切片的方式进行：

```python
lst = [1, 2, 3, 4, 5, 6]
print(lst[::-1])
```

这种方法并没有对原来的列表进行修改，而是创建了一个新列表。如果我们想对原来的列表进行改动，可以将切片后的结果赋值给 lst。也可以使用 `.reverse()` 的方法，对列表本身进行原地的反转操作：

```python
lst = [1, 2, 3, 4, 5, 6]
lst.reverse()
print(lst)

输出的内容为：[6, 5, 4, 3, 2, 1]
```

#### `.sort()` 方法

列表的 `.sort()` 方法可以将混乱的列表排序，默认是按照升序排序：

```python
lst = [2, 1, 3, 4, 6, 5]
lst.sort()
print(lst)

输出的结果为：[1, 2, 3, 4, 5, 6]
```

我们可以混合使用 reverse 方法将升序的列表反转，达到降序的目的。也可以通过将 `.sort()` 方法中的参数 reverse 设置为 True，来让列表进行降序排序：

```python
lst = [2, 1, 3, 4, 6, 5]
lst.sort(reverse=True)
print(lst)

输出的结果为：[6, 5, 4, 3, 2, 1]
```

#### `.index()` 方法

`.index()` 方法通过元素名来查找该元素在列表中的索引：

```python
lst = [1,2,3,4,6,5]
print(lst.index(4))

输出的结果为： 3
```

需要注意的是，如果需要查询的元素在列表中不止一个，`.index()` 方法只会返回从左面数第一个元素的索引值：

```python
lst = [1,2,3,4,4,6,5]
print(lst.index(4))

输出的结果仍然是： 3
```

#### 列表的加法和乘法

跟字符串类似，列表也可以进行加法和乘法的操作：

```python
lst1 = [1, 2, 3, [4]]
lst2 = [4, 5, 6]
print(lst1 + lst2)
print(lst2 * 3)

输出的结果为：
[1, 2, 3, [4], 4, 5, 6]
[4, 5, 6, 4, 5, 6, 4, 5, 6]
```

需要注意的是，列表的加法和乘法都是新开一个空间存储结果，而不是对原列表进行操作。但是加法和乘法的结果所使用的元素仍然是原列表中的。相应的操作与浅拷贝就很类似了：

```python
lst1 = [1, 2, 3, [4]]
lst2 = [4, 5, 6]
l_sum = lst1 + lst2
l_sum[3].append(9)
print(lst1, lst2, l_sum)
l_multi = lst1 * 2
l_multi[3].append(8)
print(lst1, lst2, l_multi)
```

输出的结果为：

```python
[1, 2, 3, [4, 9]] [4, 5, 6] [1, 2, 3, [4, 9], 4, 5, 6]
[1, 2, 3, [4, 9, 8]] [4, 5, 6] [1, 2, 3, [4, 9, 8], 1, 2, 3, [4, 9, 8]]
```

### 元组

元组需要补充的内容不是很多，只是需要掌握下面这三种小括号的含义即可：

```python
tu = (10)    # int，括号中只有一个元素且没有任何逗号，那就表示该元素本身。此时，括号可以看成一个运算符。
tu = (10,)    # tuple，括号中虽然只有一个元素，但是元素后面有一个逗号，表示这是一个元组。
tu = ()    # tuple，这时空元组的表示方法，括号中什么都没有。
```

### 字典

这里补充两个字典的创建方法。

#### `.fromkeys()` 方法

`.fromkeys()` 方法用来批量创建键值对，此方法需要两个参数，参数 1 是一个可迭代对象，将会迭代添加到字典中成为键，参数 2 是这些键共用的值：

```python
dic = {'key': 1, 'key1': 2}
dic1 = dic.fromkeys('abc', 12)
print(dic,dic1)
```

输出的结果为：

```python
{'key': 1, 'key1': 2} {'a': 12, 'b': 12, 'c': 12}
```

这里需要注意的是，`.fromkeys()` 方法并不是修改字典的方法，而是会创建一个新字典并返回。

另外一个需要注意的是，如果参数 2 是可变数据，所有的值会共用这个数据：

```python
l = []
dic = dict().fromkeys('abc', l)
l.append(2)
print(l, dic)
```

输出的结果为：

```python
[2] {'a': [2], 'b': [2], 'c': [2]}
```

#### 字典的创建

除了使用 fromkeys 批量创建字典之外，我们还可以使用 dict 函数将其他数据类型转成字典。dict 能够转成字典的数据类型需要是一个等长的二级容器，并且每个第二级容器中元素个数是都是 2 个。比如：

```python
data = [('a', 1), ['b', 2], {'c', 3}, {'d': 1, 4: 2}, 'e5']
print(dict(data))
```

输出的结果为：

```python
{'a': 1, 'b': 2, 3: 'c', 'd': 4, 'e': '5'}
```

上面的数据被转成了字典。我们发现，容器的数据类型可以是元组、列表、集合、字典，甚至字符串也可以。要注意的是，每个二级容器的元素个数都**只能是两个**，多了或者少了都会报错。而且因为二级容器的**第一个元素**要作为键，所以需要是**不可变，可哈希**的数据类型。

另外，需要注意的是，集合是无序的数据类型，所以键值的顺序无法保证，因此一般不会使用集合创建字典。如果使用字典创建字典，每个键值对算作一个元素，且只会将原字典的键作为数据创建字典。

### 类型转换

```python
tuple(list)
list(tuple)

set(list)
list(set)

set(tuple)
tuple(set)

int(str)  # 字符串中必须全部都是阿拉伯数字
str(int)

s1 = "".join('列表','元组')    # 将列表转换成字符串   ****
s.split(":")              # 将字符串转化成列表   ****

str(dict)
str(list)
str(tuple)
str(int)
str(set)
str(bool)
```

### 基础数据类型总结

| 数据类型 | 有序无序 | 是否可变 | 可否迭代 |   查看方式   |
| :------: | :------: | :------: | :------: | :----------: |
|   int    |   有序   |  不可变  | 不可迭代 |   直接查看   |
|   bool   |    -     |  不可变  | 不可迭代 |   直接查看   |
|   str    |   有序   |  不可变  |  可迭代  | 通过索引查看 |
|  tuple   |   有序   |  不可变  |  可迭代  | 通过索引查看 |
|   list   |   有序   |   可变   |  可迭代  | 通过索引查看 |
|   dict   |   无序   |   可变   |  可迭代  |  通过键查看  |
|   set    |   无序   |   可变   |  可迭代  | for循环查看  |