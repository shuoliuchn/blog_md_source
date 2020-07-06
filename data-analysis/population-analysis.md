## 人口分析案例

[TOC]

### 需求概述

现有美国各州数年人口普查的数据，需要进行如下分析操作：

- 导入文件，查看原始数据
- 将人口数据和各州简称数据进行合并
- 将合并的数据中重复的 abbreviation 列进行删除
- 查看存在缺失数据的列
- 找到有哪些 state/region 使得 state 的值为 NaN，进行去重操作
- 为找到的这些 state/region 的 state 项补上正确的值，从而去除掉 state 这一列的所有 NaN
- 合并各州面积数据 areas
- 我们会发现 `area(sq.mi)` 这一列有缺失数据，找出是哪些行
- 去除含有缺失数据的行
- 找出 2010 年的全民人口数据
- 计算各州的人口密度
- 排序，并找出人口密度最高的州

### 导入文件，查看原始数据

首先导入需要用到的各种模块：

```python
import numpy as np
import pandas as pd
from pandas import Series, DataFrame
```

三个文件都是 csv 格式，直接读取即可。

首先读取的是各州简称数据 `state-abbrevs.csv`：

```python
abbv = pd.read_csv('state-abbrevs.csv')
abbv.head()
```

前五行数据如下，数据中的 state 为州的全称，abbreviation 为州的简称：

```python
	state	abbreviation
0	Alabama	AL
1	Alaska	AK
2	Arizona	AZ
3	Arkansas	AR
4	California	CA
```

然后读取各州人口数据 `state-population.csv`：

```python
pop = pd.read_csv('state-population.csv')
pop.head()
```

前五行的数据如下，其中 state/region 为州的简称，ages 为年龄层次，year 为年份，population 为人口数量：

```python
	state/region	ages	year	population
0	AL	under18	2012	1117489.0
1	AL	total	2012	4817528.0
2	AL	under18	2010	1130966.0
3	AL	total	2010	4785570.0
4	AL	under18	2011	1125763.0
```

还有一个文件，各州面积数据 `state-areas.csv`，需要读取：

```python
area = pd.read_csv('state-areas.csv')
area.head()
```

前五行数据如下，其中 state 是州的全称，`area(sq.mi)` 是州的面积：

```python
	state	area (sq. mi)
0	Alabama	52423
1	Alaska	656425
2	Arizona	114006
3	Arkansas	53182
4	California	163707
```

### 将人口数据和各州简称数据进行合并

将人口数据和各州简称数据合并，只需要一条 merge 语句即可实现：

```python
abbv_pop = pd.merge(abbv, pop, left_on='abbreviation', right_on='state/region', how='outer')
abbv_pop.head()
```

合并后的数据前五行为：

```python
	state	abbreviation	state/region	ages	year	population
0	Alabama	AL	AL	under18	2012	1117489.0
1	Alabama	AL	AL	total	2012	4817528.0
2	Alabama	AL	AL	under18	2010	1130966.0
3	Alabama	AL	AL	total	2010	4785570.0
4	Alabama	AL	AL	under18	2011	1125763.0
```

### 将合并的数据中重复的 abbreviation 列进行删除

新生成的数据表中，abbreviation 和 state/region 这两列实际上是相同的，都表示的是州名简称。我们可以将 abbreviation 列删除，只保留 state/region 即可：

```python
abbv_pop.drop('abbreviation', axis=1, inplace=True)
abbv_pop.head()
```

删除掉重复列后的前五行数据为：

```python
	state	state/region	ages	year	population
0	Alabama	AL	under18	2012	1117489.0
1	Alabama	AL	total	2012	4817528.0
2	Alabama	AL	under18	2010	1130966.0
3	Alabama	AL	total	2010	4785570.0
4	Alabama	AL	under18	2011	1125763.0
```

### 查看存在缺失数据的列

数据表中行数很多，很难判断哪一列有空值。

我们可以通过 info 方法来判断某一列是否有空值：

```python
abbv_pop.info()
```

其结果为：

```python
<class 'pandas.core.frame.DataFrame'>
Int64Index: 2544 entries, 0 to 2543
Data columns (total 5 columns):
state           2448 non-null object
state/region    2544 non-null object
ages            2544 non-null object
year            2544 non-null int64
population      2524 non-null float64
dtypes: float64(1), int64(1), object(3)
memory usage: 119.2+ KB
```

总共 2544 条数据，除了 state 和 population 的其他列都有 2544 个非空数据。state 列中只有 2448 条非空数据，population 中有 2524 条数据，说明这两列中是含有空值的。

我们也可以通过 isnull 和 any 结合使用的方式，检查哪一列中有空元素：

```python
abbv_pop.isnull().any(axis=0)
```

返回的结果如下：

```python
state            True
state/region    False
ages            False
year            False
population       True
dtype: bool
```

值为 True，意味着改行有空值；值为 False，意味着改行没有空值。我们得到的结论仍然是，state 和 population 这两列中是有空值的，其他列中没有空值。

### 找到值为 NaN 的州对应的简称，并进行去重操作

找到有哪些 state/region 使得 state 的值为 NaN，将state中的空值对应的简称找到，对找到的简称进行去重操作。

首先，我们要找到 state 中为空值的那些行。方法很简单，通过 isnull 判断是否为空，然后将得到的布尔值作为索引，将所有的空值取到即可：

```python
abbv_pop.loc[abbv_pop['state'].isnull()]
```

拿到的结果为：

```python
	state	state/region	ages	year	population
2448	NaN	PR	under18	1990	NaN
2449	NaN	PR	total	1990	NaN
2450	NaN	PR	total	1991	NaN
...	...	...	...	...	...
2541	NaN	USA	total	2011	311582564.0
2542	NaN	USA	under18	2012	73708179.0
2543	NaN	USA	total	2012	313873685.0
96 rows × 5 columns
```

找到这些 state 为 NaN 的行之后，我们就可以找到它们对应的州名简称了：

```python
abbv_pop.loc[abbv_pop['state'].isnull()]['state/region']
```

这些州的简称为：

```python
2448     PR
2449     PR
2450     PR
       ... 
2541    USA
2542    USA
2543    USA
Name: state/region, Length: 96, dtype: object
```

我们看到，这里面有大量的重复的名称，需要稍微去重一下，使用 unique 方法即可：

```python
abbv_pop.loc[abbv_pop['state'].isnull()]['state/region'].unique()
```

这样，就拿到了州名为 NaN 的简称：

```python
array(['PR', 'USA'], dtype=object)
```

只有 PR 和 USA 是没有完整的名字的。

### 为没有完整州名的简称补充名字

为找到的这些 state/region 的 state 项补上正确的值，从而去除掉 state 这一列的所有 NaN。

首先，我们找到所有的 PR，然后将其全名更改为 People's Republic。这需要分两步，找到每个 PR 的索引，然后将其替换。

要找到每个 PR 的索引，只需判断内容是否为 PR，然后根据布尔值索引，拿到所有简称为 PR 的数据。最后 index 取索引即可：

```python
pr_index = abbv_pop.loc[abbv_pop['state/region'] == 'PR'].index
pr_index
```

这些索引为：

```python
Int64Index([2448, 2449, 2450, 2451, 2452, 2453, 2454, 2455, 2456, 2457, 2458,
            2459, 2460, 2461, 2462, 2463, 2464, 2465, 2466, 2467, 2468, 2469,
            2470, 2471, 2472, 2473, 2474, 2475, 2476, 2477, 2478, 2479, 2480,
            2481, 2482, 2483, 2484, 2485, 2486, 2487, 2488, 2489, 2490, 2491,
            2492, 2493, 2494, 2495],
           dtype='int64')
```

这时，只需将找打的空值填充成对应简称的全称即可。做法也很简单，根据索引和列名，也就是 state 定位，将其赋值为 People's Republic 就成了：

```python
abbv_pop.loc[pr_index, 'state'] = "People's Republic"
abbv_pop.loc[pr_index, 'state'].head()
```

替换后就成了：

```python
2448    People's Republic
2449    People's Republic
2450    People's Republic
2451    People's Republic
2452    People's Republic
Name: state, dtype: object
```

同样方法，我们也可以将 USA 的全称修改为 United States：

```python
usa_index = abbv_pop.loc[abbv_pop['state/region'] == 'USA'].index
abbv_pop.loc[usa_index, 'state'] = 'United States'
abbv_pop.loc[usa_index, 'state'].head()
```

USA 对应的 state 就被成功修改了：

```python
2496    United States
2497    United States
2498    United States
2499    United States
2500    United States
Name: state, dtype: object
```

这时，再用 info 查看：

```python
abbv_pop.info()
```

可发现，state 列已经没有空值：

```python
<class 'pandas.core.frame.DataFrame'>
Int64Index: 2544 entries, 0 to 2543
Data columns (total 5 columns):
state           2544 non-null object
state/region    2544 non-null object
ages            2544 non-null object
year            2544 non-null int64
population      2524 non-null float64
dtypes: float64(1), int64(1), object(3)
memory usage: 199.2+ KB
```

使用 isnull 结合 any 查看：

```python
abbv_pop.isnull().any(axis=0)
```

得到同样的结论：

```python
state           False
state/region    False
ages            False
year            False
population       True
dtype: bool
```

### 合并各州面积数据 areas

我们前面一直操作的是两个表，人口和州名简称表，的内容。但是我们明明读取了三张表，还有一张州面积表没有用到。这里，我们就先把州的面积合并到人口和州名表中。

很简单，一条 merge 命令即可：

```python
abbv_pop_area = pd.merge(abbv_pop, area, on='state', how='outer')
abbv_pop_area.head()
```

就拿到了合并后的数据：

```python
	state	state/region	ages	year	population	area (sq. mi)
0	Alabama	AL	under18	2012.0	1117489.0	52423.0
1	Alabama	AL	total	2012.0	4817528.0	52423.0
2	Alabama	AL	under18	2010.0	1130966.0	52423.0
3	Alabama	AL	total	2010.0	4785570.0	52423.0
4	Alabama	AL	under18	2011.0	1125763.0	52423.0
```

### 找出 area 缺失数据的行

先查看一下，新得到的总表中，那些列含有空数据：

```python
abbv_pop_area.info()
```

info 数据为：

```python
<class 'pandas.core.frame.DataFrame'>
Int64Index: 2545 entries, 0 to 2544
Data columns (total 6 columns):
state            2545 non-null object
state/region     2544 non-null object
ages             2544 non-null object
year             2544 non-null float64
population       2524 non-null float64
area (sq. mi)    2449 non-null float64
dtypes: float64(3), object(3)
memory usage: 139.2+ KB
```

使用 isnull 和 any 查看：

```python
abbv_pop_area.isnull().any(axis=0)
```

结果为：

```python
state            False
state/region      True
ages              True
year              True
population        True
area (sq. mi)     True
dtype: bool
```

我们会发现 `area(sq.mi)` 这一列有比较多的缺失数据，让我们找出是哪些行。

这实现起来很简单啦，还是老一套，判断是否为空，根据布尔值索引，拿到数据：

```python
null_area = abbv_pop_area.loc[abbv_pop_area['area (sq. mi)'].isnull()]
null_area.head()
```

成功拿到 area 为空的那些行：

```python
	state	state/region	ages	year	population	area (sq. mi)
2448	People's Republic	PR	under18	1990.0	NaN	NaN
2449	People's Republic	PR	total	1990.0	NaN	NaN
2450	People's Republic	PR	total	1991.0	NaN	NaN
2451	People's Republic	PR	under18	1991.0	NaN	NaN
2452	People's Republic	PR	total	1993.0	NaN	NaN
```

使用 index 即可拿到这些行的索引：

```python
null_area_index = null_area.index
null_area_index
```

area 为空的行为：

```python
Int64Index([2448, 2449, 2450, 2451, 2452, 2453, 2454, 2455, 2456, 2457, 2458,
            2459, 2460, 2461, 2462, 2463, 2464, 2465, 2466, 2467, 2468, 2469,
            2470, 2471, 2472, 2473, 2474, 2475, 2476, 2477, 2478, 2479, 2480,
            2481, 2482, 2483, 2484, 2485, 2486, 2487, 2488, 2489, 2490, 2491,
            2492, 2493, 2494, 2495, 2496, 2497, 2498, 2499, 2500, 2501, 2502,
            2503, 2504, 2505, 2506, 2507, 2508, 2509, 2510, 2511, 2512, 2513,
            2514, 2515, 2516, 2517, 2518, 2519, 2520, 2521, 2522, 2523, 2524,
            2525, 2526, 2527, 2528, 2529, 2530, 2531, 2532, 2533, 2534, 2535,
            2536, 2537, 2538, 2539, 2540, 2541, 2542, 2543],
           dtype='int64')
```

### 去除面积数据缺失的行

面积缺失的行索引已经找到，把它们去除十分容易，只需要 drop 即可：

```python
abbv_pop_area.drop(null_area_index, axis=0, inplace=True)
abbv_pop_area.info()
```

从 info 中可以看出，area 列已经没有空数据了：

```python
<class 'pandas.core.frame.DataFrame'>
Int64Index: 2449 entries, 0 to 2544
Data columns (total 6 columns):
state            2449 non-null object
state/region     2448 non-null object
ages             2448 non-null object
year             2448 non-null float64
population       2448 non-null float64
area (sq. mi)    2449 non-null float64
dtypes: float64(3), object(3)
memory usage: 133.9+ KB
```

### 找出 2010 年的全民人口数据

2010 年的全民人口，也就是年份为 2010，年龄段为 total 的数据。通过判断之后用布尔值索引是可以实现的。不过这里，我们还可以采用条件查询的方式，使用 query 参数进行多条件查询：

```python
abbv_pop_area.query('year == 2010 & ages == "total"')
```

成功获取到 2010 年各州的全民人口数据：

```python
	state	state/region	ages	year	population	area (sq. mi)
3	Alabama	AL	total	2010.0	4785570.0	52423.0
91	Alaska	AK	total	2010.0	713868.0	656425.0
101	Arizona	AZ	total	2010.0	6408790.0	114006.0
...
2309	West Virginia	WV	total	2010.0	1854146.0	24231.0
2394	Wisconsin	WI	total	2010.0	5689060.0	65503.0
2405	Wyoming	WY	total	2010.0	564222.0	97818.0
```

### 计算各州的人口密度

人口密度可以用公式 `总人口/面积` 来计算。

```python
abbv_pop_area['population'] / abbv_pop_area['area (sq. mi)']
```

我们就得到了人口密度的数据：

```python
0       21.316769
1       91.897221
2       21.573851
          ...    
2446     1.391135
2447     4.638103
2544          NaN
Length: 2449, dtype: float64
```

不过我们并不希望仅仅得到这么单独一列数据。我们想要把人口密度作为一个新列，添加到汇总表中，这样更便于查看。这时，只需对汇总表指定列名，将新列数据放进去即可：

```python
abbv_pop_area['density'] = abbv_pop_area['population'] / abbv_pop_area['area (sq. mi)']
abbv_pop_area.head()
```

人口密度数据就成功被添加到汇总表中：

```python
	state	state/region	ages	year	population	area (sq. mi)	density
0	Alabama	AL	under18	2012.0	1117489.0	52423.0	21.316769
1	Alabama	AL	total	2012.0	4817528.0	52423.0	91.897221
2	Alabama	AL	under18	2010.0	1130966.0	52423.0	21.573851
3	Alabama	AL	total	2010.0	4785570.0	52423.0	91.287603
4	Alabama	AL	under18	2011.0	1125763.0	52423.0	21.474601
```

### 排序，并找出人口密度最高的州

是用 sort_values 方法可以对数据进行排序：

```python
abbv_pop_area.sort_values(by='density', axis=0, ascending=False)
```

参数解释：

- by，用来指定以那一行/列进行排序
- axis，用来指定排序的方向，0 为纵向排序，1 为横向排序
- ascending，用来指定是否升序，True 为升序，False 为降序

我们就顺利拿到排序好的结果：

```python
	state	state/region	ages	year	population	area (sq. mi)	density
391	District of Columbia	DC	total	2013.0	646449.0	68.0	9506.602941
385	District of Columbia	DC	total	2012.0	633427.0	68.0	9315.102941
387	District of Columbia	DC	total	2011.0	619624.0	68.0	9112.117647
...	...	...	...	...	...	...	...
51	Alaska	AK	under18	1991.0	182180.0	656425.0	0.277534
49	Alaska	AK	under18	1990.0	177502.0	656425.0	0.270407
2544	Puerto Rico	NaN	NaN	NaN	NaN	3515.0	NaN
2449 rows × 7 columns
```

因为是按照人口密度降序排列，所以最上面的一条数据，就是对应着人口密度最高的州：

```python
abbv_pop_area.sort_values(by='density', axis=0, ascending=False).iloc[0]['state']
```

结果为：

```python
'District of Columbia'
```

符合预期。

至此，我们就完成了简单的人口数据分析。