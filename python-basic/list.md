## 列表

[TOC]

### 列表的定义

同 `整型`、`字符串` 和 `布尔值` 一样，`列表` 也是 Python 中的基本数据结构之一。`列表` 的关键字是 `list`。在 Python 中，最常用的三种用来储存数据的数据结构分别是 `字典`、`列表` 和 `字符串`，可见它是十分重要的。

列表的作用更像是一种容器，形象一点说就是我们用的书包：

1. 列表能储存大量数据
2. 列表能储存不同数据类型的数据

与列表相对照，我们先来观察一下这样一个字符串：`a = '123alexTrue'` 这个字符串中包含数字、字母和布尔值，但是表现出来的全都是字符串。我们虽然能够通过索引取到内容，但取到的值仍然是字符串。这就需要我们进行额外的判断转换操作。列表则不同，列表可以储存不同类型的数据，并且可以随时把它们直接取出来用。就像把东西装到书包里，用的时候直接取出来即可。

我们可以通过如下方法定义一个列表：

```python
lst = [1, 'alex', True]
```

列表中的每个元素以逗号分隔。各个元素以原本的数据类型存储在列表中。

列表是可变数据类型，因此可以对列表本身进行修改。与字符串方法不同，列表方法一般没有返回值，而是直接对原列表进行修改。所以一般也不需要使用变量接收返回值。

### 列表元素的增加

列表元素的增加主要有三种方法：`.append()`、`.insert()` 和 `.extend()`。

`.append()` 方法实在列表的末尾添加内容。`.append()` 的参数就是需要增加的列表元素：

```python
lst = ['Python学习手册', '核心编程']
print(lst.append("流畅的Python"))
print(lst)
```

输出的结果是：

```python
None
['Python学习手册', '核心编程', '流畅的Python']
```

`None` 在 Python 中是 `空` 的意思。`.append()` 方法的返回值为 `None`。而当我们打印列表 `lst` 时发现，列表已经被改变。这里我们可以得出结论：

- 列表是可变数据类型
- `.append()` 操作会直接对列表进行修改而不需要重新赋值

`.insert()` 方法用来向列表中插入数据。`.insert()` 方法有两个参数，`参数 1` 是要插入位置的 `索引值`，`参数 2` 是要插入的 `内容`：

```python
lst = ['Python学习手册', '核心编程']
lst.insert(1, '流畅的Python')
print(lst)
```

输出的结果是：

```python
['Python学习手册', '流畅的Python', '核心编程']
```

实际操作中，不推荐使用 `.insert()` 方法。因为一旦使用这个方法，需要把插入位置后的每一个元素都后移一位，且发生索引值的变化，有可能给项目带来不可预测的影响。

`.extend()` 方法也是在列表的最后插入内容。`.extend()` 方法的参数必须是可迭代对象。执行 `.extend()` 方法时，会将参数内容 `迭代追加` 到列表结尾：

```python
lst = ['Python学习手册', '核心编程']
lst.extend('流畅的Python')
print(lst)
```

输出的结果为：

```python
['Python学习手册', '核心编程', '流', '畅', '的', 'P', 'y', 't', 'h', 'o', 'n']
```

`.extend()` 方法用来将多个数据 *批量*添加到列表末尾。

### 列表元素的删除

列表元素的删除主要有四种方法：`.remove()`、`.pop()`、`.clear()` 和 `del`。

`.remove()` 方法可以删除指定元素名的列表元素，它的参数是要删除元素的元素名：

```python
lst = ['大圣', '海芋', '新力', '一帆', '靓仔', '石淦']
lst.remove('新力')
print(lst)
```

输出的结果为：

```python
['大圣', '海芋', '一帆', '靓仔', '石淦']
```

`.pop()` 方法如果不加参数的话，默认删除列表中的最后一个元素，并且有返回值，返回的内容是是被删除的元素。`.pop()` 是唯一一个有返回值的列表元素删除方法：

```python
lst = ['大圣', '海芋', '新力', '一帆', '靓仔', '石淦']
print(lst.pop())
print(lst)
```

输出的结果为：

```python
石淦
['大圣', '海芋', '新力', '一帆', '靓仔']
```

`.pop()` 方法也可以通过输入索引参数的方法，删除指定位置的列表元素：

```python
lst = ['大圣', '海芋', '新力', '一帆', '靓仔', '石淦']
print(lst.pop(1))
print(lst)
```

输出的结果为：

```python
海芋
['大圣', '新力', '一帆', '靓仔', '石淦']
```

`.clear()` 方法用来将列表清空，最终会得到一个空列表：

```python
lst = ['大圣', '海芋', '新力', '一帆', '靓仔', '石淦']
lst.clear()
print(lst)

输出的结果为： []
```

`del` 是用来删除的关键字。`del` 后面直接跟列表名，会将列表整个删除掉：

```python
lst = ['大圣', '海芋', '新力', '一帆', '靓仔', '石淦']
del lst
print(lst)
```

程序运行后报错：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day04/02 exercise.py", line 31, in <module>
    print(lst)
NameError: name 'lst' is not defined
```

程序报错，因为 `lst` 已经被从内从中整个移除。后来 `print` 调用 `lst` 时，找不到相应的内容，所以报错。

也可以用 `del` 删除指定索引的列表元素：

```python
lst = ['大圣', '海芋', '新力', '一帆', '靓仔', '石淦']
del lst[2]
print(lst)
```

输出的结果为：

```python
['大圣', '海芋', '一帆', '靓仔', '石淦']
```

`del`也可以通过切片来批量删除多个列表元素：

```python
lst = ['大圣', '海芋', '新力', '一帆', '靓仔', '石淦']
del lst[1:4]
print(lst)
```

输出结果为：

```python
['大圣', '靓仔', '石淦']
```

### 列表元素的修改

列表元素可以通过索引值赋值直接修改：

```python
lst = ["大圣", "海煜", "新力", "一帆", "靓仔", "石淦"]
lst[2] = '秀色可餐'
print(lst)
```

输出的结果为：

```python
['大圣', '海煜', '秀色可餐', '一帆', '靓仔', '石淦']
```

也可以通过列表的切片方法对列表元素进行批量修改：

```python
lst = ["大圣", "海煜", "新力", "一帆", "靓仔", "石淦"]
lst[1:4] = 10, 20, 30
print(lst)
```

输出的结果是：

```python
['大圣', 10, 20, 30, '靓仔', '石淦']
```

切片获取的内容空间是连续的时候，修改时内容可以多可以少；切片获取的内容空间是不连续的时候，修改时内容必须一一对应：

```python
lst = ["大圣", "海煜", "新力", "一帆", "靓仔", "石淦"]
lst[1:4] = 10, 20, 30, 40    # 切片空间连续，可多
print(lst)
lst = ["大圣", "海煜", "新力", "一帆", "靓仔", "石淦"]
lst[1:4] = 10, 20    # 切片空间连续，可少
print(lst)
lst = ["大圣", "海煜", "新力", "一帆", "靓仔", "石淦"]
lst[1:5:2] = ['alex', 'wusir']    # 切片空间不连续，必须一一对应
print(lst)
```

输出的结果为：

```python
['大圣', 10, 20, 30, 40, '靓仔', '石淦']
['大圣', 10, 20, '靓仔', '石淦']
['大圣', 'alex', '新力', 'wusir', '靓仔', '石淦']
```

### 列表元素的查看

列表元素可以通过索引查询和 for 循环遍历两种方法查看：

```python
lst = ["大圣", "海煜", "新力", "一帆", "靓仔", "石淦"]
print(lst[3])
for i in lst:
    print(i)
```

需要注意的是，进行切片操作时，获取的内容时切片的原数据本身。也就是说，切片不会改变数据类型。字符串切片后得到的还是字符串，列表切片后得到的还是列表：

```python
lst = ["大圣", "海煜", "新力", "一帆", "靓仔", "石淦"]
print(lst[1:4])

a = 'alex'
print(a[1:3])
```

输出的结果为：

```python
['海煜', '新力', '一帆']
le
```

### 列表总结

- 列表是可变数据类型，可迭代数据类型，有序的数据结构
- 列表用来储存大量数据，并且可以储存不同数据类型
- 列表就是一个容器
- 列表储存的是元素的内存地址

### 列表的嵌套

因为列表可以储存各种数据类型的数据，那么列表当然也可以储存列表，这就造成了列表的嵌套。

现在有这样一个相互嵌套的列表：

```python 
lst = [1, "alex", True, ["wusir", "污", ["嫂子","衣服多"]], ["alex", "吹nb", "熬鸡汤", ["嫂子", "教育", "从基层做起"]]]
```

如果我们想调用其中的 `"衣服多"` 这个字符串，该怎么做呢？

我们可以像下面这样逐层操作：

```python
lst1 = lst[3]
lst2 = lst1[2]
lst3 = lst2[1]
```

这样，`lst3` 就是我们想要的 `"衣服多"` 字符串。

但是这样的操作实在太过繁琐。我们发现，`lst1 = lst[3]`，既然如此，第二个式子我们可不可以把 `lst1[2]` 替换成 `lst[3][2]` 呢？那进一步说，我们可不可以直接通过 `lst[3][2][1]` 来调出 `"衣服多"` 字符串呢？

答案是完全可以的：

```python
lst = [1, "alex", True, ["wusir", "污", ["嫂子", "衣服多"]], ["alex", "吹nb", "熬鸡汤", ["嫂子", "教育", "从基层做起"]]]
print(lst[3][2][1])
print(lst[4][3][1])
```

打印出的结果是：

```python
衣服多
教育
```

像这样调用嵌套在内层列表元素的方法也被称作 `降维`，就是将类似于 `["wusir", "污", ["嫂子", "衣服多"]` 的结构当作一个元素查看。