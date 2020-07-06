# Pandas 高级操作

[TOC]

## 替换操作

替换操作可以同步作用于 Series 和 DataFrame 中。替换方法为 replace。替换操作包括单值替换和多指替换两种方式：

- 单值替换
  - 普通替换： 替换所有符合要求的元素，`to_replace=15, value='e'`
  - 按列指定单值替换： `to_replace={列标签: 替换值}, value='value'`

- 多值替换
  - 列表替换：替换列表中所有元素，`to_replace=[], value=[]`
  - 字典替换（推荐），`to_replace={to_replace: value, to_replace: value}`

首先，创建一个 5 行 6 列的数据：

```python
df = DataFrame(np.random.randint(0, 100, size=(5, 6)))
df
```

创建出来的数据为：

```python
	0	1	2	3	4	5
0	75	15	80	77	11	67
1	99	90	10	62	78	54
2	67	73	95	1	76	84
3	86	4	36	39	87	7
4	68	65	56	15	48	68
```

### 单值替换

#### 普通替换

将列表中的 4 替换成 four，就是：

```python
df.replace(to_replace=4, value='four')
```

替换完成的结果为：

```python
	0	1	2	3	4	5
0	75	15	80	77	11	67
1	99	90	10	62	78	54
2	67	73	95	1	76	84
3	86	four	36	39	87	7
4	68	65	56	15	48	68
```

有一点要注意，如果新值的数据类型发生变化，那么整个列的数据类型都会改变。比如，查看一下替换后的数据的列信息：

```
df.replace(to_replace=4, value='four').info()
```

我们看到，第二列的数据类型成了 object，也就是字符串：

```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5 entries, 0 to 4
Data columns (total 6 columns):
0    5 non-null int32
1    5 non-null object
2    5 non-null int32
3    5 non-null int32
4    5 non-null int32
5    5 non-null int32
dtypes: int32(5), object(1)
memory usage: 220.0+ bytes
```

#### 指定列替换

如果在数据表中有多个我们要替换的元素，比如 67 出现了两次。如果我们直接使用上面的方法

```python
df.replace(to_replace=67, value=666)
```

两个 67 都会被替换成 666：

```python
	0	1	2	3	4	5
0	75	15	80	77	11	666
1	99	90	10	62	78	54
2	666	73	95	1	76	84
3	86	4	36	39	87	7
4	68	65	56	15	48	68
```

如果我们只想替换某一列中的 67，可以通过字典指定列替换。其中，字典的键为要替换的数据所在列，值为要替换的数据：

```python
df.replace(to_replace={-1: 67}, value=777)    # 列索引也可以写成5，一样的
```

这样只有最后一列中的 67 被修改了，第一列的 67 仍然存在：

```python
	0	1	2	3	4	5
0	75	15	80	77	11	67
1	99	90	10	62	78	54
2	67	73	95	1	76	84
3	86	4	36	39	87	7
4	68	65	56	15	48	68
```

### 多值替换

有时候，我们要替换很多数据。如果一个一个替换，就有些麻烦。这时，我们可以通过多值替换，批量完成替换操作。

#### 列表替换

也就是把多个替换的旧值与新值分别以列表的形式传给 to_replace 和 value 参数，要注意旧值与新值要一一对应，比如：

```python
df.replace(to_replace=[1, 7], value=['one', 'seven'])
```

数据中的 1 就替换成了 one，7 替换成了 seven：

```python
	0	1	2	3	4	5
0	75	15	80	77	11	67
1	99	90	10	62	78	54
2	67	73	95	one	76	84
3	86	4	36	39	87	seven
4	68	65	56	15	48	68
```

#### 字典替换（推荐）

列标替换虽然可以实现多值替换，但是看起来并不直观，可读性不高。我们可以将旧值与新值以字典的键值对形式，传给 to_replace：

```python
df.replace(to_replace={15: 'piu', 95: 'pia'})
```

同样实现了多值替换：

```python
	0	1	2	3	4	5
0	75	piu	80	77	11	67
1	99	90	10	62	78	54
2	67	73	pia	1	76	84
3	86	4	36	39	87	7
4	68	65	56	piu	48	68
```

## 映射操作

概念：创建一个映射关系列表，把 values 元素和一个特定的标签或者字符串绑定（给一个元素值提供不同的表现形式）。

map 只是 Serise 的函数，只能由 Series 调用。

创建一个 df，两列分别是姓名和薪资，然后给其名字起对应的英文名：

```python
dic = {
    'name': ['张三', '李四', '张三'],
    'salary': [15000, 20000, 15000],
}
df = DataFrame(data=dic)
df
```

创建出来的数据为：

```python
	name	salary
0	张三	15000
1	李四	20000
2	张三	15000
```

然后我们用字典创建一个映射关系表，制定了映射的对照关系。通过这个映射关系，就可以实现映射：

```python
map_dic = {
    '张三': 'Tom',
    '李四': 'Jerry',
}
df['nickname'] = df['name'].map(map_dic)
df
```

实际上，map 就是将原数据中的内容，用字典的键对应的值替换掉了：

```python
	name	salary	nickname
0	张三	15000	Tom
1	李四	20000	Jerry
2	张三	15000	Tom
```

## 运算工具

### Series 的 map 方法

需求：还是使用上面的数据，假设超过 3000 部分的钱需要缴纳 50% 的税，计算每个人的税后薪资。

我们可以通过定义一个计算税后薪资的函数，然后使用 map 方法获取税后薪水数据：

```python
def after_tax(salary):
    if salary > 3000:
        salary -= (salary - 3000) * 0.5
    return salary
df['after_tax'] = df['salary'].map(after_tax)
df
```

得到的新的一列 after_tax 就是税后的薪水了：

```python
	name	salary	nickname	after_tax
0	张三	15000	Tom	9000.0
1	李四	20000	Jerry	11500.0
2	张三	15000	Tom	9000.0
```

### DataFrame 中的 apply 操作

apply 是 df 的中运算工具，将运算作用到 df 的行或者列中。

首先创建一个 5 行 3 列的 DataFrame：

```python
df = DataFrame(np.random.randint(0, 100, size=(5, 3)))
df
```

得到的数据为：

```python
	0	1	2
0	80	17	87
1	23	36	2
2	41	28	99
3	3	72	61
4	16	47	12
```

使用 df 的 apply，即可对每一列进行指定的操作：

```python
def my_add(num):
    # 这里的num为df中的每一列，或者说每一个Series
    # 既然是列，四则运算是可以的，但是复杂的操作无法运算
    # print(num)    # 如果搞不懂，打印出来就看明白了
    return num + 10
df.apply(my_add, axis=0)
```

每一列的所有元素都增加了 10：

```python
	0	1	2
0	90	27	97
1	33	46	12
2	51	38	109
3	13	82	71
4	26	57	22
```

apply 方法将会把 df 中的每一行/列传入到运算函数中。我们可以对这一行/列数据进行四则运算，但是不能进行更复杂的运算操作。当然，因为是以行/列传入，我们可以对数据进行一些聚合运算。

### DataFrame 的 applymap 方法

applymap 可以将某种运算作用到 df 的每一个元素中：

```python
def my_add(num):
    # 这里的num为df中的每一个数据
    # 可以对这个数据进行更复杂的运算
    # print(num)    # 如果搞不懂，打印出来就看明白了
    return num + 10
df.applymap(my_add)
```

每个元素都增加了 10：

```python
	0	1	2
0	90	27	97
1	33	46	12
2	51	38	109
3	13	82	71
4	26	57	22
```

## 排序实现的随机抽样

有的时候，如果数据量很大的话，对它们全部进行运算是不现实的。这时，我们可以从中抽取一部分数据，降低运算量。

随机抽样的思路是先将原来的数据的顺序打乱，也就是进行随机排序，然后提取新数据中的前几行。

将数据打乱，我们可以用到 DataFrame 的两个方法：

- `take()`，可以根据指定的索引取样
- `np.random.permutation(n)`，用来创建从 0 到 n - 1 顺序随机的数组

首先创建一些数据：

```python
df = DataFrame(np.random.randint(0, 100, size=(100, 3)), columns=['A', 'B', 'C'])
df
```

然后，指定索引进行取样，比如取索引为 4、11、2、7 的几行数据：

```python
df.take(indices=[4, 11, 2, 7], axis=0)
```

- indices 用来指定我们需要的数据的索引，注意这里**只能用隐式索引**
- axis 用来指定方向，与 drop 系列相同，0 为横向取样，1 为纵向取样

我们就成功拿到这些行的数据：

```python
	A	B	C
4	37	95	30
11	73	73	16
2	83	60	43
7	42	41	11
```

对列取样道理相同，只是把 axis 指定为 1 即可。注意不能用显式索引。就不举例子了。

不难想象，如果我们有一个随机的索引数组，通过 take 取样，即可实现对数据的重新排列。

怎么获得顺序随机的索引数组呢？可以使用 `np.random` 的 `permutation` 方法来生成：

```python
np.random.permutation(5)
```

这样就成了一组随机顺序的索引数组：

```python
array([1, 3, 0, 2, 4])
```

因为我们的数据有 100 行，只需生成 100 个随机索引数组，然后用这个数组进行取样，即可得到随机顺序的数据：

```python
df.take(np.random.permutation(100))
```

我们就实现了对原数据的随机排序：

```python
	A	B	C
94	53	74	56
66	51	32	54
19	85	62	49
86	59	70	93
...	...	...	...
20	28	26	33
82	97	96	0
84	5	17	48
2	83	60	43
100 rows × 3 columns
```

然后我们提取其中前几条，即实现了对原数据的随机取样，比如只提取前 10 条数据：

```python
df.take(np.random.permutation(100))[:10]
```

就拿到了随机的 10 条数据，因为又运行了一次随机取样，所以数据跟上面的是不同的：

```python
	A	B	C
55	97	63	69
64	23	69	99
70	18	1	5
10	10	28	47
51	87	24	28
3	80	52	92
84	5	17	48
38	12	99	22
67	76	11	65
83	51	91	24
```

## 数据的分类分组处理

在 MySQL 中有一个分组操作，可以用来对某一类数据进行聚合运算等处理。类似地，DataFrame 数据也可以进行分组操作，使用的方法也是 groupby。

数据分类处理的核心：

- `groupby()` 函数分组
- `groups` 属性查看分组情况

首先，创建数据：

```python
df = DataFrame({
    'item': ['Apple', 'Banana', 'Orange', 'Banana', 'Orange', 'Apple'],
    'price': [4, 3, 3, 2.5, 4,2],
    'color': ['red', 'yellow', 'yellow', 'green', 'green', 'green'],
    'weight': [12, 20, 50, 30, 20, 44]
})
df
```

创建出来的数据就是：

```python
	color	item	price	weight
0	red	Apple	4.0	12
1	yellow	Banana	3.0	20
2	yellow	Orange	3.0	50
3	green	Banana	2.5	30
4	green	Orange	4.0	20
5	green	Apple	2.0	44
```

使用 group 对水果种类 item 进行分组：

```python
df.groupby('item')
```

分组得到了对象：

```python
<pandas.core.groupby.DataFrameGroupBy object at 0x000001A2D3D741D0>
```

我们可以通过 groups 方法查看 group 对象的信息：

```python
df.groupby('item').groups
```

我们看到，总共分成了三组，按照水果名分配的。每个水果的索引也记录在里面。

```python
{'Apple': Int64Index([0, 5], dtype='int64'),
 'Banana': Int64Index([1, 3], dtype='int64'),
 'Orange': Int64Index([2, 4], dtype='int64')}
```

我们可以对分组对象使用 mean 方法，计算平均值：

```python
df.groupby('item').mean()
```

只有数值型数据才能进行这类聚合操作，所以我们只得到了每种水果价格和重量的平均值，颜色信息被舍弃掉了。水果种类成了每一行的显式索引：

```python
	price	weight
item		
Apple	3.00	28
Banana	2.75	25
Orange	3.50	35
```

如果我们只想获取价格信息，可以对结果的列进行索引取值：

```python
df.groupby('item').mean()['price']
```

得到了水果价格的平均值信息：

```python
item
Apple     3.00
Banana    2.75
Orange    3.50
Name: price, dtype: float64
```

但是我们一般不这么计算分组的平均值。因为这样做，会对分组中说有的列都进行聚合运算（这里是求平均值），将会消耗很多的资源。

更推荐的方式是将分组数据先索引取值，然后再进行聚合求平均值：

```python
df.groupby('item')['price'].mean()
```

结果还是一样的，只不过这样只会对价格列进行运算，极大节省了资源：

```python
item
Apple     3.00
Banana    2.75
Orange    3.50
Name: price, dtype: float64
```

光拿到均价并不能让我们满足，我们还需要将这些均值合并到原来的数据表中。我们可以通过映射的方法来实现：

```python
mean_price = df.groupby('item')['price'].mean()
price_dict = mean_price.to_dict()
df['mean_price'] = df['item'].map(price_dict)
df
```

映射需要用到映射关系表，一般是个字典。这个字典的键就是数据表中的需要关联的数据，值就是要映射成为的值。通过 Series 的 to_dict 方法，刚好可以生成这样的对应关系。于是，平均值就成功添加到数据表中：

```python
	color	item	price	weight	mean_price
0	red	Apple	4.0	12	3.00
1	yellow	Banana	3.0	20	2.75
2	yellow	Orange	3.0	50	3.50
3	green	Banana	2.5	30	2.75
4	green	Orange	4.0	20	3.50
5	green	Apple	2.0	44	3.00
```

同样地，如果我们想要把每种颜色的水果的平均重量放到数据表中，实现分组聚合操作：

```python
mean_weight = df.groupby('color')['weight'].mean()
weight_dict = mean_weight.to_dict()
df['mean_weight'] = df['color'].map(weight_dict)
df
```

合并后的数据为：

```python
	color	item	price	weight	mean_price	mean_weight
0	red	Apple	4.0	12	3.00	12.000000
1	yellow	Banana	3.0	20	2.75	35.000000
2	yellow	Orange	3.0	50	3.50	35.000000
3	green	Banana	2.5	30	2.75	31.333333
4	green	Orange	4.0	20	3.50	31.333333
5	green	Apple	2.0	44	3.00	31.333333
```

## 高级数据聚合

使用 groupby 分组后，也可以使用 transform 和 apply 提供自定义函数实现更多的运算

- `df.groupby('item')['price'].sum()` <===> `df.groupby('item')['price'].apply(sum)`
- transform 和 apply 都会进行运算，在 transform 或者 apply 中传入函数即可
- transform 和 apply 也可以传入一个 lambda 表达式

仍旧使用上面的数据，将每一种水果的价格提升平均值的 15 个百分点，将提升后的价格汇总到源数据中：

```python
def add_price(num):
    # num是每一个分组的数据，可对其进行四则运算和聚合操作
    return num + num.mean()
df['new_price'] = df.groupby('item')['price'].apply(add_price)
df
```

得到的结果为：

```python
	color	item	price	weight	mean_price	mean_weight	new_price
0	red	Apple	4.0	12	3.00	12.000000	7.00
1	yellow	Banana	3.0	20	2.75	35.000000	5.75
2	yellow	Orange	3.0	50	3.50	35.000000	6.50
3	green	Banana	2.5	30	2.75	31.333333	5.25
4	green	Orange	4.0	20	3.50	31.333333	7.50
5	green	Apple	2.0	44	3.00	31.333333	5.00
```

我们也可以使用 transform 方法达成同样的任务：

```python
df.groupby('item')['price'].transform(lambda num: num + num.mean())
```

这里我用匿名函数代替了原来的函数，都是一样的，结果为：

```python
0    7.00
1    5.75
2    6.50
3    5.25
4    7.50
5    5.00
Name: price, dtype: float64
```

如需和并到原数据中，直接合并即可。

## 数据加载

### 文本数据读取

read_csv 可以用来读取文本文件中的数据。文本文件一般以 csv 或 txt 为后缀。

比如，读取 `type-.txt` 文件中的数据：

```python
data = pd.read_csv('data/type-.txt')
data
```

读取来的数据为：

```python
	你好-我好-他也好
0	也许-大概-有可能
1	然而-未必-不见得
```

很显然，这是一个两行一列的数据。但是我们希望它以 `-` 分隔，且第一行不要是表头，从而形成一个三行三列的数据。我们只需要指定分隔符 sep 为 `-`，表头 header 为 None 即可：

```python
data = pd.read_csv('data/type-.txt', sep='-', header=None)
data
```

成功获得了三行三列的数据：

```python
	0	1	2
0	你好	我好	他也好
1	也许	大概	有可能
2	然而	未必	不见得
```

我们还可以通过 names 参数来指定列索引：

```python
data = pd.read_csv('data/type-.txt', sep='-', header=None, names=['lala', 'haha', 'papa'])
data
```

生成的列中就都有了索引：

```python
	lala	haha	papa
0	你好	我好	他也好
1	也许	大概	有可能
2	然而	未必	不见得
```

### 数据库数据读取

#### 连接数据库获取数据

Pandas 可以直接使用关系型数据库（如：MySQL、Oracle、SQLite，等）的连接导入数据。

例如，导入 SQLite 中的数据：

```python
import sqlite3
# 这里的conn是SQLite的连接，如果用MySQL，将其替换即可
conn = sqlite3.connect('data/weather_2012.sqlite')
sql_df = pd.read_sql('select * from weather_2012', conn)
sql_df
```

顺利读取到 SQLite 中的数据：

```python
	index	Date/Time	Temp (C)	Dew Point Temp (C)	Rel Hum (%)	Wind Spd (km/h)	Visibility (km)	Stn Press (kPa)	Weather
0	0.0	2012-01-01 00:00:00	-1.8	-3.9	86.0	4.0	8.0	101.24	Fog
1	1.0	2012-01-01 01:00:00	-1.8	-3.7	87.0	4.0	8.0	101.24	Fog
2	2.0	2012-01-01 02:00:00	-1.8	-3.4	89.0	7.0	4.0	101.26	Freezing Drizzle,Fog
...	...	...	...	...	...	...	...	...	...
8783	8783.0	2012-12-31 23:00:00	0.0	-2.1	86.0	30.0	11.3	99.89	Snow
8784	NaN	None	NaN	NaN	NaN	NaN	NaN	NaN	Fog
8785	NaN	None	NaN	NaN	NaN	NaN	NaN	NaN	Fog
8786 rows × 9 columns
```

类似地，我们也可以使用 pymysql 读取 MySQL 中的数据：

```python
import pymysql
conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', password='123', database='school')
sql_df = pd.read_sql('select * from student', conn)
sql_df
```

成功读取到数据：

```python
	sid	sname	gender	class_id
0	1	钢蛋	女	1
1	2	铁锤	女	1
2	3	山炮	男	2
```

#### 将数据存储到数据库

使用 DataFrame 的 to_sql 命令可以将之前用到的存放商品信息的 df 中的数据值写入存储到数据库中：

```python
conn = sqlite3.connect('data/weather_2012.sqlite')
df.to_sql('goods_data', conn)
```

查看数据库中的数据：

```python
pd.read_sql('select * from goods_data', conn)
```

发现数据已经成功写入到数据库中：

```python
	index	color	item	price	weight	mean_price	mean_weight	new_price
0	0	red	Apple	4.0	12	3.00	12.000000	7.00
1	1	yellow	Banana	3.0	20	2.75	35.000000	5.75
2	2	yellow	Orange	3.0	50	3.50	35.000000	6.50
3	3	green	Banana	2.5	30	2.75	31.333333	5.25
4	4	green	Orange	4.0	20	3.50	31.333333	7.50
5	5	green	Apple	2.0	44	3.00	31.333333	5.00
```

## 透视表

透视表是一种可以对数据动态排布并且分类汇总的表格格式。或许大多数人都在 Excel 使用过数据透视表，也体会到它的强大功能，而在 pandas 中它被称作 pivot_table。

透视表的优点有很多：

- 灵活性高，可以随意定制你的分析计算要求
- 脉络清晰易于理解数据
- 操作性强，报表神器

pivot_table 有四个最重要的参数：index、values、columns、aggfunc，接下来我们逐个介绍一下。

首先读取文件中的数据：

```python
df = pd.read_csv('./data/透视表-篮球赛.csv', encoding='utf8', engine='python')
df
```

因为文件中有中文，所以需要指定编码。文件路径中出现中文，可能会报错，这时候可以尝试指定 engine 为 python 解决。

读取到的数据为：

```python
	对手	胜负	主客场	命中	投篮数	投篮命中率	3分命中率	篮板	助攻	得分
0	勇士	胜	客	10	23	0.435	0.444	6	11	27
1	国王	胜	客	8	21	0.381	0.286	3	9	27
2	小牛	胜	主	10	19	0.526	0.462	3	7	29
3	灰熊	负	主	8	20	0.400	0.250	5	8	22
4	76人	胜	客	10	20	0.500	0.250	3	13	27
5	黄蜂	胜	客	8	18	0.444	0.400	10	11	27
6	灰熊	负	客	6	19	0.316	0.222	4	8	20
7	76人	负	主	8	21	0.381	0.429	4	7	29
8	尼克斯	胜	客	9	23	0.391	0.353	5	9	31
9	老鹰	胜	客	8	15	0.533	0.545	3	11	29
10	爵士	胜	主	19	25	0.760	0.875	2	13	56
11	骑士	胜	主	8	21	0.381	0.429	11	13	35
12	灰熊	胜	主	11	25	0.440	0.429	4	8	38
13	步行者	胜	客	9	21	0.429	0.250	5	15	26
14	猛龙	负	主	8	25	0.320	0.273	6	11	38
15	太阳	胜	客	12	22	0.545	0.545	2	7	48
16	灰熊	胜	客	9	20	0.450	0.500	5	7	29
17	掘金	胜	主	6	16	0.375	0.143	8	9	21
18	尼克斯	胜	主	12	27	0.444	0.385	2	10	37
19	篮网	胜	主	13	20	0.650	0.615	10	8	37
20	步行者	胜	主	8	22	0.364	0.333	8	10	29
21	湖人	胜	客	13	22	0.591	0.444	4	9	36
22	爵士	胜	客	8	19	0.421	0.333	5	3	29
23	开拓者	胜	客	16	29	0.552	0.571	8	3	48
24	鹈鹕	胜	主	8	16	0.500	0.400	1	17	26
```

index 参数：分类汇总的分类条件。

每个 pivot_table 必须拥有一个 index。如果想查看哈登对阵每个队伍的得分则需要对每一个队进行分类并计算其各类得分的平均值。

首先，需要拿到哈登对阵同一对手在不同主客场下的数据，分类条件为对手和主客场：

```python
df.pivot_table(index=['对手', '主客场'])
```

同 groupby 分组类似，除了 index，我们只拿到了数值型的数据：

```python

3分命中率	助攻	命中	得分	投篮命中率	投篮数	篮板
对手	主客场							
76人	主	0.4290	7.0	8.0	29.0	0.381	21.0	4.0
	客	0.2500	13.0	10.0	27.0	0.500	20.0	3.0
勇士	客	0.4440	11.0	10.0	27.0	0.435	23.0	6.0
国王	客	0.2860	9.0	8.0	27.0	0.381	21.0	3.0
太阳	客	0.5450	7.0	12.0	48.0	0.545	22.0	2.0
小牛	主	0.4620	7.0	10.0	29.0	0.526	19.0	3.0
尼克斯	主	0.3850	10.0	12.0	37.0	0.444	27.0	2.0
	客	0.3530	9.0	9.0	31.0	0.391	23.0	5.0
开拓者	客	0.5710	3.0	16.0	48.0	0.552	29.0	8.0
掘金	主	0.1430	9.0	6.0	21.0	0.375	16.0	8.0
步行者	主	0.3330	10.0	8.0	29.0	0.364	22.0	8.0
	客	0.2500	15.0	9.0	26.0	0.429	21.0	5.0
湖人	客	0.4440	9.0	13.0	36.0	0.591	22.0	4.0
灰熊	主	0.3395	8.0	9.5	30.0	0.420	22.5	4.5
	客	0.3610	7.5	7.5	24.5	0.383	19.5	4.5
爵士	主	0.8750	13.0	19.0	56.0	0.760	25.0	2.0
	客	0.3330	3.0	8.0	29.0	0.421	19.0	5.0
猛龙	主	0.2730	11.0	8.0	38.0	0.320	25.0	6.0
篮网	主	0.6150	8.0	13.0	37.0	0.650	20.0	10.0
老鹰	客	0.5450	11.0	8.0	29.0	0.533	15.0	3.0
骑士	主	0.4290	13.0	8.0	35.0	0.381	21.0	11.0
鹈鹕	主	0.4000	17.0	8.0	26.0	0.500	16.0	1.0
黄蜂	客	0.4000	11.0	8.0	27.0	0.444	18.0	10.0
```

values 参数：需要对计算的数据进行筛选。

如果我们只需要哈登在主客场和不同胜负情况下的得分、篮板与助攻三项数据：

```python
df.pivot_table(index=['主客场', '胜负'], values=['得分', '篮板', '助攻'])
```

我们就得到了简化的数据：

```python
		助攻	得分	篮板
主客场	胜负			
主	胜	10.555556	34.222222	5.444444
	负	8.666667	29.666667	5.000000
客	胜	9.000000	32.000000	4.916667
	负	8.000000	20.000000	4.000000
```

Aggfunc 参数：设置我们对数据聚合时进行的函数操作

当我们未设置 aggfunc 时，它默认 `aggfunc='mean'` 计算均值。也就是说，我们上面的数据其实是平均值。

如果我们想获得 James Harden 在主客场和不同胜负情况下的总得分、总篮板、总助攻时：

```python
df.pivot_table(index=['主客场', '胜负'], values=['得分', '篮板', '助攻'], aggfunc='sum')
```

我们就得到了总合的数据：

```python
		助攻	得分	篮板
主客场	胜负			
主	胜	95	308	49
	负	26	89	15
客	胜	108	384	59
	负	8	20	4
```

更高端的写法是，我们可以给每一列指定一个聚合函数：

```python
df.pivot_table(index=['主客场', '胜负'], aggfunc={'得分': 'sum', '篮板': 'mean', '助攻': 'std'})
```

结果就是：

```python
		助攻	得分	篮板
主客场	胜负			
主	胜	3.205897	308	5.444444
	负	2.081666	89	5.000000
客	胜	3.618136	384	4.916667
	负	NaN	20	4.000000
```

columns：可以设置列层次字段

columns 参数用来对 values 字段进行分类。

通过前面的学习，我们很容易可以获取所有队主客场的总得分：

```python
df.pivot_table(index='主客场', values='得分', aggfunc='sum')
```

数据也成功拿到了：

```python
	得分
主客场	
主	397
客	404
```

但是这个数据太笼统，我们希望看到的是每个队主客场的总得分（在总得分的基础上又进行对手的分类），这就可以指定 columns 参数了：

```python
df.pivot_table(index='主客场', values='得分', aggfunc='sum', columns='对手')
```

我们就得到了每个队伍主客场得分的数据：

```python
对手	76人	勇士	国王	太阳	小牛	尼克斯	开拓者	掘金	步行者	湖人	灰熊	爵士	猛龙	篮网	老鹰	骑士	鹈鹕	黄蜂
主客场																		
主	29.0	NaN	NaN	NaN	29.0	37.0	NaN	21.0	29.0	NaN	60.0	56.0	38.0	37.0	NaN	35.0	26.0	NaN
客	27.0	27.0	27.0	48.0	NaN	31.0	48.0	NaN	26.0	36.0	49.0	29.0	NaN	NaN	29.0	NaN	NaN	27.0
```

如果不喜欢这里的 NaN， 我们还可以通过指定 fill_value 参数的方式将其替换：

```python
df.pivot_table(index='主客场', values='得分', columns='对手', aggfunc='sum', fill_value=0)
```

这样得到的数据就没有 NaN 了：

```python
对手	76人	勇士	国王	太阳	小牛	尼克斯	开拓者	掘金	步行者	湖人	灰熊	爵士	猛龙	篮网	老鹰	骑士	鹈鹕	黄蜂
主客场																		
主	29	0	0	0	29	37	0	21	29	0	60	56	38	37	0	35	26	0
客	27	27	27	48	0	31	48	0	26	36	49	29	0	0	29	0	0	27
```

## 交叉表

交叉表是一种用于计算分组的特殊透视图，用于对数据进行汇总。需要注意的是，crosstab 是 Pandas 的方法，而非 DataFrame 的。

交叉表就是一种特殊的透视表，只不过将聚合函数指定为了 count。交叉表能实现的，透视表都可以实现。

- `pd.crosstab(index, colums)`
  - index：分组数据，交叉表的行索引
  - columns：交叉表的列索引

第一件事，当然是构造数据：

```python
df = DataFrame({
    'sex': ['man', 'man', 'women', 'women', 'man', 'women', 'man', 'women', 'women'],
    'age': [15, 23, 25, 17, 35, 57, 24, 31, 22],
    'smoke': [True, False, False, True, True, False, False, True, False],
    'height': [168, 179, 181, 166, 173, 178, 188, 190, 160]
})
df
```

数据是这样的：

```python
	age	height	sex	smoke
0	15	168	man	True
1	23	179	man	False
2	25	181	women	False
3	17	166	women	True
4	35	173	man	True
5	57	178	women	False
6	24	188	man	False
7	31	190	women	True
8	22	160	women	False
```

使用交叉表求出各个性别抽烟的人数：

```python
pd.crosstab(df.sex, df.smoke)
```

得到的结果为：

```python
smoke	False	True
sex		
man	2	2
women	3	2
```

求出各个年龄段抽烟人情况：

```python
pd.crosstab(df.smoke, df.age)
```

得到的结果就是：

```python
age	15	17	22	23	24	25	31	35	57
smoke									
False	0	0	1	1	1	1	0	0	1
True	1	1	0	0	0	0	1	1	0
```

