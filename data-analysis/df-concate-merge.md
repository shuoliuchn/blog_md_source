# DataFrame 的级联与合并操作

[TOC]

有的时候，我们的数据可能会来自不同的文件。读取每个文件时，会产生一个 DataFrame。如果我们想要综合这些文件中的数据，就需要将多个表格整合成一个大表格。就类似于我们使用 MySQL 数据库，对于不同表进行的联表操作一样。

要将多个 DataFrame 整合成一个大的综合的 DataFrame，就需要使用到 DataFrame 的级联与合并操作。

## 级联操作

在 NumPy 的学习中，我们已经见识过了级联操作。当时使用的方法是 concatenate。级联操作，就是将两个 DataFrame 按照行/列的索引合并到一起。如果有共同的索引，则将共同索引的数据放在同一行/列。对于非共同索引，则会补空。

DataFrame 提供了两种级联操作的方法：

- `pd.concat`
- `pd.append`

首先，创建两个 DataFrame 数据。注意，两个列的索引是不一致的：

```python
df1 = DataFrame(data=np.random.randint(0, 100, (5, 3)), columns=['A', 'B', 'C'])
df2 = DataFrame(data=np.random.randint(0, 100, (5, 3)), columns=['A', 'D', 'C'])
```

得到的 df1 为：

```python
	A	B	C
0	27	60	52
1	14	38	13
2	30	74	29
3	17	97	99
4	76	32	63
```

df2 为：

```python
	A	D	C
0	68	15	47
1	40	78	93
2	67	12	13
3	62	99	59
4	32	34	49
```

### `pd.concate`

pandas使用pd.concat函数，与np.concatenate函数类似，只是多了一些参数：

- `objs`，用于指定要级联的 DataFrame
- `axis=0`，0 为纵向级联，1 为横向级联
- `keys`
- `join='outer' / 'inner'`：表示的是级联的方式，outer 会将所有的项进行级联（忽略匹配和不匹配），而 inner 只会将匹配的项级联到一起，不匹配的不级联
- `ignore_index=False`

#### 匹配级联

匹配级联，也就是级联方向的索引是完全一致的，级联操作只需简单地按照索引合并匹配即可。比如自己与自己合并，一定是级联匹配的咯：

```python
pd.concat((df1, df1, df1), axis=1)
```

结果就是三个 df1 横向排布的新 DataFrame：

```python
	A	B	C	A	B	C	A	B	C
0	27	60	52	27	60	52	27	60	52
1	14	38	13	14	38	13	14	38	13
2	30	74	29	30	74	29	30	74	29
3	17	97	99	17	97	99	17	97	99
4	76	32	63	76	32	63	76	32	63
```

同样，也可以进行纵向级联：

```python
pd.concat((df2, df2), axis=0)
```

得到了两个 df2 纵向合并的 DataFrame：

```python
	A	D	C
0	68	15	47
1	40	78	93
2	67	12	13
3	62	99	59
4	32	34	49
0	68	15	47
1	40	78	93
2	67	12	13
3	62	99	59
4	32	34	49
```

#### 不匹配级联

不匹配指的是级联的维度的索引不一致。例如纵向级联时列索引不一致，横向级联时行索引不一致

DataFrame 的不匹配级联有 2 种连接方式：

- 外连接 `join=outer`：补 NaN（默认模式）
- 内连接 `join=inner`：只连接匹配的项

如果想要保留数据的完整性必须使用 outer（外连接）。

刚刚已经提到了 df1 和 df2 的列索引是不一致的，如果将他们纵向级联，就会涉及到不匹配级联：

```python
pd.concat((df1, df2), axis=0, join='outer')
```

join 为 outer，是外连接，即不匹配的索引会补 Nan：

```python
	A	B	C	D
0	27	60.0	52	NaN
1	14	38.0	13	NaN
2	30	74.0	29	NaN
3	17	97.0	99	NaN
4	76	32.0	63	NaN
0	68	NaN 	47	15.0
1	40	NaN 	93	78.0
2	67	NaN 	13	12.0
3	62	NaN 	59	99.0
4	32	NaN 	49	34.0
```

如果把 join 设置为 inner：

```python
pd.concat((df1, df2), axis=0, join='inner')
```

不匹配的索引将会被舍弃掉：

```python
	A	C
0	27	52
1	14	13
2	30	29
3	17	99
4	76	63
0	68	47
1	40	93
2	67	13
3	62	59
4	32	49
```

我们看到，使用内连接会丢失很多数据。多数时候，为了保证数据的完整性，我们会使用外连接，也就是把 join 的值设置为 outer。

### append

如果是纵向的级联，更方便的级联方式是使用 append。注意，append 只能将两个 DataFrame 纵向级联起来。

对于非匹配级联，默认会对不一致的索引位置补空。

使用 append 进行纵向级联的操作十分简单：

```python
df1.append(df1)
```

得到的结果是两个 df1 纵向级联的数据：

```python
	A	B	C
0	27	60	52
1	14	38	13
2	30	74	29
3	17	97	99
4	76	32	63
0	27	60	52
1	14	38	13
2	30	74	29
3	17	97	99
4	76	32	63
```

对于非匹配级联的情况：

```python
df1.append(df2)
```

索引不一致的列会默认补空：

```python
	A	B	C	D
0	27	60.0	52	NaN
1	14	38.0	13	NaN
2	30	74.0	29	NaN
3	17	97.0	99	NaN
4	76	32.0	63	NaN
0	68	NaN 	47	15.0
1	40	NaN 	93	78.0
2	67	NaN 	13	12.0
3	62	NaN 	59	99.0
4	32	NaN 	49	34.0
```

## 合并操作

merge 与 concat 的区别在于，merge 需要依据某一共同列来进行合并。

使用 `pd.merge()` 合并时，会自动根据两者相同 column 名称的那一列，作为 key 来进行合并。

注意每一列元素的顺序不要求一致。

### 一对一合并

首先，构造数据 df1：

```python
df1 = DataFrame(data={
    'employee': ['Bob', 'Jake', 'Lisa'],
    'group': ['Accounting', 'Engineering', 'Engineering'],
})
df1
```

df1 的数据为：

```python
	employee	group
0	Bob		Accounting
1	Jake	Engineering
2	Lisa	Engineering
```

然后，构造数据 df2：

```python
df2 = DataFrame(data={
    'employee': ['Lisa', 'Bob', 'Jake'],
    'hire_date': [2004, 2008, 2012],
})
df2
```

df2 的数据为：

```python
	employee	hire_date
0	Lisa	2004
1	Bob 	2008
2	Jake	2012
```

我们看到，df1 和 df2 有共同的列，employee。不过在两个 DataFrame 中的每一行数据顺序并不一致。如果使用级联操作，那么这两组数据回直接合并在一块，出现两个 employee 不说，每个数据并不能一一对应。我们希望的是，Lisa 的 hire_data 和她的 group 出现在同一行。

对于这种需求，我们可以使用合并操作来实现：

```python
pd.merge(df1, df2, on='employee')
```

on 参数用来指定合并操作所参照的列。合并好的数据为：

```python
	employee	group	hire_date
0	Bob 	Accounting	2008
1	Jake	Engineering	2012
2	Lisa	Engineering	2004
```

我们看到，合并好的数据中，只有一个 employee 列，而且每个人的 group 和 hire_data 都出现在自己名字的那一行。这和 MySQL 的多表的联表查询十分相似。

### 一对多合并

在一对一合并中，共同列中的元素个数是相同且一一对应的。但是真实的数据可能未必这样。有的时候，两个 DataFrame 虽然有共同的列，列中的元素也确实差不多，但是有的 DataFrame 中，每个元素都只有一个，没有重复的，而在另一个 DataFrame 中，会出现重复元素，且这些重复元素在前一个 DataFram 中都存在。这样这个列中就不是一一对应的关系。还是可以参照 MySQL 多表操作的一对多联表操作。

这时，操作上和一对一合并没有什么不同。只是结果上，没有重复元素的那个 DataFrame 中的行会复制多个，和有重复元素的 DataFrame 对应上。

接下来，我们演示一下一对多合并的操作。

首先，还是创建两组数据。df3 中，没有重复元素：

```python
df3 = DataFrame({
    'Employee': ['Lisa', 'Jake'],
    'group': ['Accounting', 'Engineering'],
    'hire_date': [2004, 2016],
})
df3
```

df3 的数据为：

```python
	Employee	group	hire_date
0	Lisa	Accounting	2004
1	Jake	Engineering	2016
```

然后是 df4：

```python
df4 = DataFrame({
    'group': ['Accounting', 'Engineering', 'Engineering'],
    'supervisor': ['Carly', 'Guido', 'Steve']
})
df4
```

df4 的数据为：

```python
	group	supervisor
0	Accounting	Carly
1	Engineering	Guido
2	Engineering	Steve
```

我们把 df3 和 df4 合并，就是：

```python
pd.merge(df3, df4)    # on不写，默认的合并条件就是两张表中公有的列索引
```

合并后的结果为：

```python
	employee	group	hire_date	supervisor
0	Lisa	Accounting	2004	Carly
1	Jake	Engineering	2016	Guido
2	Jake	Engineering	2016	Steve
```

我们看到，employee 列的 Jake 被复制了一份，分给了两个 Engineering group。

如果两个 DataFrame 中存在共同索引，则不必指定 on，会默认以共同索引合并。

### 多对多合并

一对一合并和一对多合并都要求，在合并的参考列中，其中一个 DataFrame 的数据在另一个 DataFrame 中都存在。但是对于实际的数据中，尤其是数据量很大的情况，很可能会出现两张表中各自都有对方没有的数据的情况。

我们先创建两个数据。我们依旧使用上面的 df1：

```python
df1 = DataFrame(data={
    'employee': ['Bob', 'Jake', 'Lisa'],
    'group': ['Accounting', 'Engineering', 'Engineering'],
})
df1
```

df1 的数据为：

```python
	employee	group
0	Bob 	Accounting
1	Jake	Engineering
2	Lisa	Engineering
```

然后再来一个 df5：

```python
df5 = DataFrame({
    'group': ['Engineering', 'Engineering', 'HR'],
    'supervisor': ['Carly', 'Guido', 'Steve'],
})
df5
```

df5 的数据为：

```python
	group   	supervisor
0	Engineering	Carly
1	Engineering	Guido
2	HR      	Steve
```

我们看到，df1 和 df5 有一个相同的列，group。但是 df1 的 group 中，有一个 Accounting，这在 df5 中是不存在的。同样，df5 中 group 列的 HR 是不存在与 df1 中的。

这时，如果我们直接使用 merge 方法，合并这两个 DataFrame 的话：

```python
pd.merge(df1, df5)
```

就会发现，结果是这样的：

```python
	employee	group	supervisor
0	Jake	Engineering	Carly
1	Jake	Engineering	Guido
2	Lisa	Engineering	Carly
3	Lisa	Engineering	Guido
```

合并后的结果仅保留了 df1 和 df5 共有的部分，它们独自的那部分数据默认全都被舍弃掉了。

而它们相同的那部分数据，以笛卡尔积的形式生成。

如果我们想要保留它们独有的行，需要指定 how 的参数为 outer：

```python
pd.merge(df1, df5, how='outer')
```

两组数据中的独有行全都被保留，没有的数据补空：

```python
	employee	group	supervisor
0	Bob 	Accounting	NaN
1	Jake	Engineering	Carly
2	Jake	Engineering	Guido
3	Lisa	Engineering	Carly
4	Lisa	Engineering	Guido
5	NaN 	HR      	Steve
```

如果我们只想保留左侧的数据，只需将 how 设置为 left：

```python
pd.merge(df1, df5, how='left')
```

位于左侧的 df1 中有 Accounting，被保留了，而 df5 中的 HR 没有保留：

```python
	employee	group	supervisor
0	Bob 	Accounting	NaN
1	Jake	Engineering	Carly
2	Jake	Engineering	Guido
3	Lisa	Engineering	Carly
4	Lisa	Engineering	Guido
```

如果只想保留右侧的数据，可以将 how 设置为 right：

```python
pd.merge(df1, df5, how='right')
```

这样，只有右侧的 df5 中的 HR 被保留，而 Accounting 别舍弃：

```python
	employee	group	supervisor
0	Jake	Engineering	Carly
1	Lisa	Engineering	Carly
2	Jake	Engineering	Guido
3	Lisa	Engineering	Guido
4	NaN 	HR      	Steve
```

## key 的规范化

### 多个同名列，使用 on 指定列名

当列冲突时，即有多个列名称相同时，需要使用 on 参数来指定哪一个列作为 key，配合 suffixes 指定冲突列名。

首先，创建两个数据表。数据表 df6：

```python
df6 = DataFrame({
    'employee': ['Jack', 'Summer', 'Steve'],
    'group': ['Accounting', 'Finace', 'Marketing'],
})
df6
```

df6 的数据为：

```python
	employee	group
0	Jack	Accounting
1	Summer	Finace
2	Steve	Marketing
```

然后是数据表 df7：

```python
df7 = DataFrame({
    'employee': ['Jack', 'Bob', 'Jake'],
    'hire_date': [2003, 2009, 2012],
    'group': ['Accounting', 'Sell', 'CEO'],
})
df7
```

df7 的数据为：

```python
	employee	group	hire_date
0	Jack	Accounting	2003
1	Bob 	Sell    	2009
2	Jake	CEO     	2012
```

可以看到，两组数据都有 employee 和 group 列。如果我们还是使用 merge 方法，直接合并的话：

```python
pd.merge(df6, df7, how='outer')
```

所有同名列都会进行合并：

```python
	employee	group	hire_date
0	Jack	Accounting	2003.0
1	Summer	Finace  	NaN
2	Steve	Marketing	NaN
3	Bob 	Sell    	2009.0
4	Jake	CEO     	2012.0
```

可是有的时候，两张表中的列名字虽然相同，但含义是有差异的。比如，group 的确指代同一个含义，我们可以依照它来合并，但是 employee 是有不同含义的，我们需要区分开来。这时，我们就需要指定 on 参数为我们要合并的列的列名，这里就是 group：

```python
pd.merge(df6, df7, on='group', how='outer')
```

这样，我们合并后的新表中，两个 employee 就被区分开了：

```python
	employee_x	group	employee_y	hire_date
0	Jack	Accounting	Jack	2003.0
1	Summer	Finace  	NaN 	NaN
2	Steve	Marketing	NaN 	NaN
3	NaN 	Sell    	Bob 	2009.0
4	NaN 	CEO     	Jake	2012.0
```

默认情况下，两个 employee 以后缀 `_x` 和 `_y` 区分。我们可以使用 suffixes 参数指定后缀名：

```python
pd.merge(df6, df7, on='group', how='outer', suffixes=['a', 'b'])
```

这时，合并后的表格就成了：

```python
	employeea	group	employeeb	hire_date
0	Jack	Accounting	Jack	2003.0
1	Summer	Finace  	NaN 	NaN
2	Steve	Marketing	NaN 	NaN
3	NaN 	Sell    	Bob 	2009.0
4	NaN 	CEO     	Jake	2012.0
```

### 无同名列，使用 `left_on` 和 `right_on`

之前讨论的合并都是两组数据中，只要有一列是同名的。可是如果所有列都不同名，该如何做呢？

首先，我们还是创建两个没有任何同名列的 DataFrame。首先是 df8：

```python
df8 = DataFrame({
    'employee': ['Bob', 'Linda', 'Bill'],
    'group': ['Accounting', 'Product', 'Marketing'],
    'hire_data': [1998, 2017, 2018],
})
df8
```

df8 的数据为：

```python
	employee	group	hire_data
0	Bob 	Accounting	1998
1	Linda	Product 	2017
2	Bill	Marketing	2018
```

然后是 df9：

```python
df9 = DataFrame({
    'name': ['Lisa', 'Bob', 'Bill'],
    'hire_dates': [1998, 2016, 2007],
})
df9
```

df9 的数据为：

```python
	hire_dates	name
0	1998	Lisa
1	2016	Bob
2	2007	Bill
```

我们看见，df8 和 df9 中没有任何相同名字的列。这时，如果直接使用 merge 进行合并的话，会报错。我们需要使用 `left_on` 和 `right_on` 参数指定数据合并时需要参照的列，比如 name 和 employ 本来的含义是相同的，我们可以以这两列为基准：

```python
pd.merge(df8, df9, left_on='employee', right_on='name', how='outer')
```

合并后的结果为：

```python
	employee	group	hire_data	hire_dates	name
0	Bob 	Accounting	1998.0	2016.0	Bob
1	Linda	Product 	2017.0	NaN 	NaN
2	Bill	Marketing	2018.0	2007.0	Bill
3	NaN 	NaN     	NaN 	1998.0	Lisa
```

