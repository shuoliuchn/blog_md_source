## Pandas 的基础操作

[TOC]

### Pandas 初识

既然 NumPy 已经可以帮助我们进行数据的处理了，我们为什么还要学习 Pandas 呢？

原来，NumPy 主要用来帮助我们处理的是数值型的数据，当然在数据分析中除了数值型的数据还有好多其他类型的数据（字符串，时间序列）， Pandas 就可以帮我们很好地处理除了数值型的其他数据！

学习 pandas 我们主要是学习其中的两个常用的类：

- Series
- DataFrame（重点）

导包方式：

```python
from pandas import Series, DataFrame
import pandas as pd
import numpy as np
```

### Series

#### Series 的创建

Series 是一种类似于一维数组的对象，由下面两个部分组成：

- values：一组数据（ndarray 类型）
- index：相关的数据索引标签

Series 可以通过两种方式来创建：

1. 由列表或一维 numpy 数组创建
2. 由字典创建

由列表创建 Series：

```python
s = Series(data=[1, 2, 3])    # 索引是0，1，2的默认形式：隐式索引
s
```

输出的结果为：

```python
0    1
1    2
2    3
dtype: int64
```

右侧的一列是我们的数据，左侧的那一列则是索引。在不指定的情况下，使用的是隐式索引。

列表创建显式索引的 Series：

```python
s = Series(data=[1, 2, 3], index=['a', 'b', 'c'])
s
# a,b,c索引叫做显式索引,不会覆盖原有的隐式索引
# 显式索引可以增加数据的可读性
```

输出的结果为：

```python
a    1
b    2
c    3
dtype: int64
```

要注意，Series 的数据必须是一维的，否则会报错。

除了使用列表，我们也可以通过 array 创建 Series：

```python
s= Series(data=np.random.randint(0, 100, size=(4,)))
s
```

创建出来的 Series 为：

```python
0    59
1    88
2    99
3    37
dtype: int32
```

列表创建的 Series 默认是隐式索引，因为列表是无法指定显示索引的。如果我们使用字典作为数据源创建 Series，键将会用作显式索引，值则为数据：

```python
data = {
    '语文': 100,
    '数学': 120,
    '英语': 120,
}
s = Series(data=data)    # 字典的key作为Seres的显示索引
s
```

这样生成的 Series 为：

```python
语文    100
数学    120
英语    120
dtype: int64
```

数据成功加入，键作为了 Series 的索引。

#### Series 的索引和切片

Series 的隐式索引是永远可用的，显式索引只有在指定了的时候才可用。

Series 的索引可以像列表一样中括号取值，也可以像类属性一样用点取值，甚至还可以一次取多个值，使用方法十分灵活多样。

我们用的数据还是刚刚通过字典创建的 Series：

```python
数学    120
英语    120
语文    100
dtype: int64
```

索引演示：

```python
s[0]    # 120    隐式索引取值
s['语文']    # 100    显式索引取值
s.英语    # 120    点取值
s[['语文', '数学']]    # 取多值
# 取多值的结果为：
语文    100
数学    120
dtype: int64
```

Series 的切片和普通列表一样，只是多了显示索引切片的方法。

隐式索引切片和普通列表使用方法相同，顾头不顾尾：

```python
s[0:2]
```

切片的结果为：

```python
数学    120
英语    120
dtype: int64
```

显示索引切片与列表略有不同，切片结果首位兼得：

```python
s['英语': '语文']
```

切片的结果为：

```python
英语    120
语文    100
dtype: int64
```

#### Series 的常用属性

Series 的常用属性有：

- shape
- size
- index
- values

使用方式如下：

```python
s.shape    # (3,) Series的结构形状
s.size    # 3 Series的尺寸，也就是元素个数
s.index    # Index(['数学', '英语', '语文'], dtype='object') 索引
s.values    # array([120, 120, 100], dtype=int64) 值
```

#### Series 的常用方法

- `head()` 和 `tail()`
- `unique()` 和 `nunique()`
- `isnull()` 和 `notnull()`
- `add()`、`sub()`、`mul()` 和 `div()`

首先创建一个 Series：

```python
s = Series(data=[1, 1, 2, 2, 3, 4, 5, 6, 6, 7])
s
```

head 方法可以查看前 n 个数据，默认取前五个：

```python
s.head(5)
```

输出的结果为：

```
0    1
1    1
2    2
3    2
4    3
dtype: int64
```

tail 方法用来查看后 n 个数据，默认取后五个：

```python
s.tail(3)
```

就拿到了后 3 条数据：

```python
7    6
8    6
9    7
dtype: int64
```

unique 用于给 Series 去重：

```python
s.unique()
```

注意返回的是数组，而不是 Series：

```python
array([1, 2, 3, 4, 5, 6, 7], dtype=int64)
```

nunique 可以查看 Series 中非重复元素的总个数：

```
s.nunique()    # 7
```

在这里先讨论一下 Series 的算术运算。Series 的运算法则是索引一致的元素进行算数运算否则补空。例如：

```python
s1 = Series(data=[1, 2, 3], index=['a', 'b', 'c'])
s2 = Series(data=[1, 2, 3], index=['a', 'd', 'c'])
s = s1 + s2
s
```

注意在 s1 中没有索引为 d 的数据，而 s2 中没有索引为 b 的数据。所以两个 Series 加和之后，得到的新 Series中，b 和 d 对应的数据为空：

```python
a    2.0
b    NaN
c    6.0
d    NaN
dtype: float64
```

isnull 方法可以判断 Series 中的元素是否为空：

```python
s.isnull()
```

返回的结果为：

```python
a    False
b     True
c    False
d     True
dtype: bool
```

notnull 方法用来判断 Series 中的元素是否非空：

```
s.notnull()
```

返回的结果为：

```python
a     True
b    False
c     True
d    False
dtype: bool
```

### DataFrame

DataFrame 是一个 `表格型` 的数据结构。DataFrame 由按一定顺序排列的多列数据组成。设计初衷是将 Series 的使用场景从一维拓展到多维。DataFrame 既有行索引，也有列索引。

- 行索引：index
- 列索引：columns
- 值：values

#### DataFrame 的创建

DataFrame 同样有两种创建方式：

- ndarray 或列表创建
- 字典创建

使用 ndarray 创建 DataFrame：

```python
df = DataFrame(data=[[1, 2, 3], [4, 5, 6]], index=['a', 'b'], columns=['A', 'B', 'C'])
df
```

创建出来的 DataFrame 是表格形式的数据：

```python
	A	B	C
a	1	2	3
b	4	5	6
```

还可以通过 ndarray 创建 DataFrame：

```python
df = DataFrame(data=np.random.randint(0, 100, (6, 8)))
df
```

如果不指定显性索引，将使用隐式索引：

```python
	0	1	2	3	4	5	6	7
0	82	31	53	64	75	48	72	32
1	54	37	20	29	73	52	23	38
2	17	14	13	60	24	69	27	36
3	37	8	51	18	77	58	56	32
4	29	13	41	31	95	6	66	67
5	14	39	5	29	79	62	42	90
```

我们同样可以通过字典来创建 DataFrame：

```python
data = {
    'A': {'a': 1, 'b': 2},
    'B': {'a': 3, 'b': 4},
}
df = DataFrame(data=data)
df
```

创建出来的 DataFrame 就是：

```python
	A	B
a	1	3
b	2	4
```

我们也可以在字典中部分指定显式索引，然后将另外的显式索引作为参数传递进去：

```python
data = {
    '张三': [150, 150, 150, 150],
    '李四': [0, 0, 0, 0],
}
df = DataFrame(data=data, index=['语文', '数学', '英语', '理综'])
df
```

创建出来的 DataFrame 就是：

```python
	张三	李四
语文	150	0
数学	150	0
英语	150	0
理综	150	0
```

#### DataFrame 的属性

- values
- columns
- index
- shape

这里仍然使用刚刚创建的 DataFrame 数据：

```python
	张三	李四
语文	150	0
数学	150	0
英语	150	0
理综	150	0
```

这些属性的用法如下：

```python
df.values    # 查看DataFrame中所有的值，返回的是数组
# 查看值的结果：
array([[150,   0],
       [150,   0],
       [150,   0],
       [150,   0]], dtype=int64)

df.index     # Index(['语文', '数学', '英语', '理综'], dtype='object') 横向索引
df.columns    # Index(['张三', '李四'], dtype='object') 纵向索引
df.shape    # (4, 2)
df.size    # 8
```

#### DataFrame 的索引和切片

首先，创建一个 5 行 4 列的数据，用来后面的操作：

```python
df = DataFrame(data=np.random.randint(0, 100, (5, 4)), columns=['A', 'B', 'C', 'D'], index=['a', 'b', 'c', 'd', 'e'])
df
```

生成的数据为：

```python
	A	B	C	D
a	31	2	57	84
b	88	18	64	81
c	35	5	48	71
d	69	57	86	44
e	19	92	8	68
```

##### DataFrame 的索引操作

DataFrame 的索引操作分为三个方面：

- 对行进行索引
- 对列进行索引
- 对元素进行索引

对列进行索引是最简单直观的，直接通过索引取即可：

```python
df['A']
```

成功取到 A 列：

```python
a    31
b    88
c    35
d    69
e    19
Name: A, dtype: int32
```

我们同样可以取多列：

```python
df[['B', 'D']]
```

取到的结果为：

```python
	B	D
a	2	84
b	18	81
c	5	71
d	57	44
e	92	68
```

索引取行要用到 loc 和 iloc。其中，loc 用来进行显式索引取行，iloc 用来进行隐式索引取行。

```python
df.loc['a']    # 显式索引取行
df.iloc[3]    # 隐式索引取行
```

虽然两种方式返回的数据不同，但格式是一致的，我就只放一个隐式索引的结果：

```python
A    69
B    57
C    86
D    44
Name: d, dtype: int32
```

同样，可以索引取多行：

```python
df.loc[['b', 'e']]    # 显式索引取多行
df.iloc[[3, 4]]    # 隐式索引取多行
```

同样只放一个隐士索引的结果：

```python
	A	B	C	D
d	69	57	86	44
e	19	92	8	68
```

给定两个坐标，可以定位到 DataFrame 中的元素：

```python
df.loc['b', 'D']    # 81
df.iloc[2, 3]    # 71
```

##### DataFrame 的切片操作

切片操作包括两部分：

- 对行进行切片
- 对列进行切片

与索引操作相反，DataFrame 对行切片可以直接操作。同 Series 一样，隐式索引切片是顾头不顾尾的：

```python
df[2:4]
```

切片结果为：

```python
	A	B	C	D
c	35	5	48	71
d	69	57	86	44
```

也可以用显示索引切片，这回是顾头又顾尾了：

```python
df['b': 'd']
```

切片结果为：

```python
	A	B	C	D
b	88	18	64	81
c	35	5	48	71
d	69	57	86	44
```

DataFrame 对列进行切片需要使用 loc 和 iloc 方法。

使用显式索引对列切片：

```python
df.loc[:, 'B': 'C']
```

切片结果为：

```python
	B	C
a	2	57
b	18	64
c	5	48
d	57	86
e	92	8
```

使用隐式索引切片：

```python
df.iloc[:, 1:3]
```

切片结果同上。

##### DataFrame 索引和切片操作总结

索引：

- `df[col]` 取列
- `df.loc[index]` 取行
- `df.iloc[index,col]` 取元素

切片：

- `df[index1:index3]` 切行
- `df.iloc[:,col1:col3]` 切列

#### DataFrame 的运算

同 Series。

