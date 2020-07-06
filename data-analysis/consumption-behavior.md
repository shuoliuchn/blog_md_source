# 用户消费行为分析案例

电商平台每天都会产生大量的用户购买商品的消费行为数据。通过分析这些数据，我们可以了解电商的运营情况，用户的消费规律等信息。有了这些信息，我们可以完善平台建设，预测用户的消费行为，从而提高用户体验。

首先导入数据分析三剑客模块：

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from pandas import Series, DataFrame
```

## 数据类型处理

### 数据加载

数据以文本形式保存在 `CDNOW_master.txt` 中，其中每一列的字段名称及含义为：

- user_id：用户 ID
- order_dt：购买日期
- order_product：购买产品的数量
- order_amount：购买金额

```python
data = pd.read_csv('CDNOW_master.txt', header=None, sep='\s+', names=['user_id', 'order_dt', 'order_product', 'order_amount'])
data.head()
```

要注意，这里面的数据之间以不同数目的空格分隔，所以 sep 不能写死了，而是要写成正则。另外，因为没有表头，所以要设置 header 为 None。

生成的数据前五行为：

```python
	user_id	order_dt	order_product	order_amount
0	1	19970101	1	11.77
1	2	19970112	1	12.00
2	2	19970112	5	77.00
3	3	19970102	2	20.76
4	3	19970330	2	20.76
```

### 观察数据

查看数据的数据类型，并查看数据中是否存储在缺失值：

```python
data.info()
```

数据全都为数值（整数或浮点数），且没有缺失数据：

```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 69659 entries, 0 to 69658
Data columns (total 4 columns):
user_id          69659 non-null int64
order_dt         69659 non-null int64
order_product    69659 non-null int64
order_amount     69659 non-null float64
dtypes: float64(1), int64(3)
memory usage: 2.1 MB
```

将 order_dt 转换成时间类型：

```
data['order_dt'] = pd.to_datetime(data['order_dt'], format='%Y%m%d')
data.head()
```

to_datetime 转换的时间显示格式会到毫秒，我们只需要精确到天即可，format 就是用来指定输出的格式的：

```python
	user_id	order_dt	order_product	order_amount
0	1	1997-01-01	1	11.77
1	2	1997-01-12	1	12.00
2	2	1997-01-12	5	77.00
3	3	1997-01-02	2	20.76
4	3	1997-03-30	2	20.76
```

查看 data 的信息：

```python
data.info()
```

order_dt 列已经成了时间类型：

```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 69659 entries, 0 to 69658
Data columns (total 4 columns):
user_id          69659 non-null int64
order_dt         69659 non-null datetime64[ns]
order_product    69659 non-null int64
order_amount     69659 non-null float64
dtypes: datetime64[ns](1), float64(1), int64(2)
memory usage: 2.1 MB
```

查看数据的统计描述

- 计算所有用户购买商品的平均数量
- 计算所有用户购买商品的平均花费

只需要使用 describe 方法，即可查看这些常用的统计信息：

```python
data.describe()
```

相应的统计信息为，平均数量为 2.41，平均花费为 35.89：

```python
	user_id	order_product	order_amount
count	69659.000000	69659.000000	69659.000000
mean	11470.854592	2.410040	35.893648
std	6819.904848	2.333924	36.281942
min	1.000000	1.000000	0.000000
25%	5506.000000	1.000000	14.490000
50%	11410.000000	2.000000	25.980000
75%	17273.000000	3.000000	43.700000
max	23570.000000	99.000000	1286.010000
```

在源数据中添加一列表示月份，可以通过 `astype('datetime64[M]')` 方法：

```python
data['order_month'] = data['order_dt'].values.astype('datetime64[M]')
data.head()
```

values 不一定需要，但是不加的话有可能会报错。

新的数据前五行为：

```python
	user_id	order_dt	order_product	order_amount	order_month
0	1	1997-01-01	1	11.77	1997-01-01
1	2	1997-01-12	1	12.00	1997-01-01
2	2	1997-01-12	5	77.00	1997-01-01
3	3	1997-01-02	2	20.76	1997-01-01
4	3	1997-03-30	2	20.76	1997-03-01
```

虽然月份的数据后面都跟了个 1 号，但不影响后续的计算。

## 按月数据分析

所有用户每月花费的总金额，绘制曲线图展示

首先拿到数据，只需要对月份进行分组，然后就可得到每月所有用户的总花费了：

```python
data.groupby('order_month')['order_amount'].sum()
```

数据如下：

```python
order_month
1997-01-01    299060.17
1997-02-01    379590.03
1997-03-01    393155.27
1997-04-01    142824.49
1997-05-01    107933.30
1997-06-01    108395.87
1997-07-01    122078.88
1997-08-01     88367.69
1997-09-01     81948.80
1997-10-01     89780.77
1997-11-01    115448.64
1997-12-01     95577.35
1998-01-01     76756.78
1998-02-01     77096.96
1998-03-01    108970.15
1998-04-01     66231.52
1998-05-01     70989.66
1998-06-01     76109.30
Name: order_amount, dtype: float64
```

我们可以直接使用 Pandas 绘制线型图：

```python
data.groupby('order_month')['order_amount'].sum().plot()
```

绘制出了图像：

![img](consumption-behavior.assets/Tue,%2007%20Apr%202020%20210123.png)

我们也可以通过 Matplotlib 绘制图像：

```python
plt.figure(figsize=(8, 8))
plt.plot(data.groupby('order_month')['order_amount'].sum())
plt.xlabel('order_month')
plt.ylabel('order_amount')
```

图像为：

![img](consumption-behavior.assets/Tue,%2007%20Apr%202020%20211812.png)

从图像中可以看出，1997 年 1 到 3 月是这个电商平台的巅峰时期。但是貌似经营不善，或者因为前期有很大补贴，后期补贴减少优惠力度降低，1997 年 3 月以后，用户的消费行为急剧下降，最后稳定在一个水平上下。

所有用户每月的产品购买量，计算方法跟花费总金额是相似的，只是要计算的是商品数量了而已：

```python
plt.figure(figsize=(8, 8))
plt.plot(data.groupby('order_month')['order_product'].sum())
plt.xlabel('order_month')
plt.ylabel('order_product')
```

图形和总金额的十分相似：

![img](consumption-behavior.assets/Tue,%2007%20Apr%202020%20212416.png)

所有用户每月的消费总次数。每一条数据即为一次消费，只需查询每月有多少条数据即可：

```python
data.groupby('order_month')['user_id'].count()
```

得到的数据为：

```python
order_month
1997-01-01     8928
1997-02-01    11272
1997-03-01    11598
1997-04-01     3781
1997-05-01     2895
1997-06-01     3054
1997-07-01     2942
1997-08-01     2320
1997-09-01     2296
1997-10-01     2562
1997-11-01     2750
1997-12-01     2504
1998-01-01     2032
1998-02-01     2026
1998-03-01     2793
1998-04-01     1878
1998-05-01     1985
1998-06-01     2043
Name: user_id, dtype: int64
```

统计每月的消费人数。这个注意不能用消费时间统计，因为存在某个用户当天多次消费的情况。我们需要统计每个月有多少个独立的用户购买了商品：

```python
data.groupby('order_month')['user_id'].nunique()
```

每月消费人数的数据为：

```python
order_month
1997-01-01    7846
1997-02-01    9633
1997-03-01    9524
1997-04-01    2822
1997-05-01    2214
1997-06-01    2339
1997-07-01    2180
1997-08-01    1772
1997-09-01    1739
1997-10-01    1839
1997-11-01    2028
1997-12-01    1864
1998-01-01    1537
1998-02-01    1551
1998-03-01    2060
1998-04-01    1437
1998-05-01    1488
1998-06-01    1506
Name: user_id, dtype: int64
```

## 用户个体消费数据分析

所有用户消费总金额和消费总购买量的统计描述：

```python
data['order_amount'].sum()    # 2500315.6300000004 消费总金额
data['order_product'].sum()    # 167881 总购买量
```

各个用户消费金额和消费产品数量的散点图：

```python
user_total = data.groupby('user_id').sum()
plt.figure(figsize=(8, 8))
plt.scatter(user_total['order_product'], user_total['order_amount'])
plt.xlabel('product')
plt.ylabel('amount')
```

画出来的散点图为：

![img](consumption-behavior.assets/Tue,%2007%20Apr%202020%20220710.png)

各个用户消费总金额的直方分布图（消费金额在 1000 之内的分布）：

```python
plt.hist(data.groupby('user_id').sum().query('order_amount <= 1000')['order_amount'], bins=50)
```

画出的直方图为：

![img](consumption-behavior.assets/Tue,%2007%20Apr%202020%20221129.png)

各个用户消费的总数量的直方分布图（消费商品的数量在 100 次之内的分布）：

```python
plt.hist(data.groupby('user_id').sum().query('order_product <= 100')['order_product'], bins=30)
```

画出来的直方图为：

![img](consumption-behavior.assets/Tue,%2007%20Apr%202020%20221438.png)

## 用户消费行为分析

### 用户消费时间统计

用户第一次消费的月份分布，和人数统计，绘制线形图。

用户第一次消费的时间是该用户消费时间的最小值，因为可能很多用户都会在同一个月份买东西，可以通过 unique 去重：

```
data.groupby('user_id')['order_month'].min().unique()
```

就得到了消费的月份：

```python
array(['1997-01-01T00:00:00.000000000', '1997-02-01T00:00:00.000000000',
       '1997-03-01T00:00:00.000000000'], dtype='datetime64[ns]')
```

我们也可以统计每月第一次购买的人数：

```python
data.groupby('user_id')['order_month'].min().value_counts()
```

每月第一次购买的人数数据为：

```python
1997-02-01    8476
1997-01-01    7846
1997-03-01    7248
Name: order_month, dtype: int64
```

可以画出线形图：

```python
data.groupby('user_id')['order_month'].min().value_counts().plot()
```

图像就是这样的：

![img](consumption-behavior.assets/Tue,%2007%20Apr%202020%20224718.png)

用户最后一次消费的时间分布，和人数统计，并绘制线形图。

这个跟第一次的数据很相似了，只需要把时间的最小值换成最大值即可。

首先看最后一次消费的时间分布：

```python
data.groupby('user_id')['order_month'].max().unique()
```

用户最后一次购买的时间分布就很分散了：

```python
array(['1997-01-01T00:00:00.000000000', '1998-05-01T00:00:00.000000000',
       '1997-12-01T00:00:00.000000000', '1998-01-01T00:00:00.000000000',
       '1998-03-01T00:00:00.000000000', '1998-06-01T00:00:00.000000000',
       '1998-02-01T00:00:00.000000000', '1997-09-01T00:00:00.000000000',
       '1997-06-01T00:00:00.000000000', '1997-03-01T00:00:00.000000000',
       '1998-04-01T00:00:00.000000000', '1997-02-01T00:00:00.000000000',
       '1997-11-01T00:00:00.000000000', '1997-10-01T00:00:00.000000000',
       '1997-04-01T00:00:00.000000000', '1997-05-01T00:00:00.000000000',
       '1997-08-01T00:00:00.000000000', '1997-07-01T00:00:00.000000000'],
      dtype='datetime64[ns]')
```

类似地，我们也可以求出每月最后一次消费的用户数目：

```python
data.groupby('user_id')['order_month'].max().value_counts()
```

每个月份，最后一次购买的用户数目为：

```python
1997-02-01    4912
1997-03-01    4478
1997-01-01    4192
1998-06-01    1506
1998-05-01    1042
1998-03-01     993
1998-04-01     769
1997-04-01     677
1997-12-01     620
1997-11-01     609
1998-02-01     550
1998-01-01     514
1997-06-01     499
1997-07-01     493
1997-05-01     480
1997-10-01     455
1997-09-01     397
1997-08-01     384
Name: order_month, dtype: int64
```

我们也可以画出线形图：

```python
data.groupby('user_id')['order_month'].max().value_counts().plot()
```

线形图为：

![img](consumption-behavior.assets/Tue,%2007%20Apr%202020%20225250.png)

### 新老客户的占比

我们认为，仅消费一次的用户为新用户，消费多次的用户为老用户

分析出每一个用户的第一个消费和最后一次消费的时间。

我们可以通过 `agg(['func1','func2'])` 方法对分组后的结果进行指定多种形式的聚合：

```python
first_last_dt = data.groupby('user_id')['order_dt'].agg(['min', 'max'])
first_last_dt.head()
```

用户首次和最后一次消费的时间数据前五条为：

```python
	min	max
user_id		
1	1997-01-01	1997-01-01
2	1997-01-12	1997-01-12
3	1997-01-02	1998-05-28
4	1997-01-01	1997-12-12
5	1997-01-01	1998-01-03
```

分析出新老客户的消费比例。

如果该用户第一次消费的时间等于最后一次消费的时间，说明他只消费了一次，我们就认为他是新用户；如果用户第一次消费的时间不等于最后一次消费的时间，则说明他消费了多次，我们认为他是老用户。根据这个性质，我们就可以计算出新用户和老用户的数量，进而计算出新老客户的比例了：

```python
(first_last_dt['min'] == first_last_dt['max']).value_counts()
```

根据刚刚的分析，结果中 True 的数目为新用户的数目，False 的数目为老用户的数目：

```python
True     12054
False    11516
dtype: int64
```

比例也就一目了然。

### 用户分层

分析得出每个用户的总购买量和总消费金额以及最近一次消费的时间的表格 rfm

RFM 模型设计：

- R 表示客户最近一次交易时间的间隔。
  - `/ np.timedelta64(1, 'D')`：去除时间单位 days
- F 表示客户购买商品的总数量。F 值越大，表示客户交易越频繁，反之则表示客户交易不够活跃。
- M 表示客户交易的金额。M 值越大，表示客户价值越高，反之则表示客户价值越低。
- 将 R，F，M 作用到 rfm 表中

总数量和总金额很容易计算到。因为数据比较简单，除了 user_id，只有商品数量和消费金额是数值型数据，所以得到的结果我们甚至不需要索引取列：

```python
rfm = data.groupby('user_id').sum()
rfm.head()
```

直接拿到每个用户购买商品总数目和交易总金额：

```python
	order_product	order_amount
user_id		
1	1	11.77
2	6	89.00
3	16	156.46
4	7	100.50
5	29	385.61
```

至于最近一次时间间隔，我们可以通过所有数据中的最有一次交易减去每个人的最后一次交易时间得到，而每个人的最后一次交易时间，可以通过他的所有交易时间的最大值获取：

```python
rfm['order_dt'] = (data['order_dt'].max() - data.groupby('user_id')['order_dt'].max()) / np.timedelta64(1, 'D')
rfm.head()
```

rmf 数据为：

```python
	order_product	order_amount	order_dt
user_id			
1	1	11.77	545.0
2	6	89.00	534.0
3	16	156.46	33.0
4	7	100.50	200.0
5	29	385.61	178.0
```

将列索引规范为 RFM：

```python
rfm.columns = ['F', 'M', 'R']
rfm.head()
```

规范的 RFM 数据前五行为：

```python
	F	M	R
user_id			
1	1	11.77	545.0
2	6	89.00	534.0
3	16	156.46	33.0
4	7	100.50	200.0
5	29	385.61	178.0
```

根据价值分层，可将用户分为如下几类：

- 重要价值客户
- 重要保持客户
- 重要挽留客户
- 重要发展客户
- 一般价值客户
- 一般保持客户
- 一般挽留客户
- 一般发展客户

使用已有的分层模型即可通过 rfm_func 算法函数计算出客户价值：

```python
# rfm分层算法
def rfm_func(x):    # -6.122656	-94.310426	177.778362 ==>'0','0','1'
    # 存储存储的是三个字符串形式的0或者1
    level = x.map(lambda x :'1' if x >= 0 else '0')
    label = level['R'] + level.F + level.M    # '100'
    
    d = {
        '111':'重要价值客户',
        '011':'重要保持客户',
        '101':'重要挽留客户',
        '001':'重要发展客户',
        '110':'一般价值客户',
        '010':'一般保持客户',
        '100':'一般挽留客户',
        '000':'一般发展客户'
    }
    result = d[label]    # d['100']
    # result= '一般挽留客户'
    return result
```

只需要将我们 rfm 数据的每一层数据通过运算工具传递到 rmf 分层算法函数中，即可获得该层用户的价值，将价值整合到 rmf 数据中即可。在传递之前，还要给每一个数据减去该列的均值：

```python
rfm['label'] = rfm.apply(lambda x: x - x.mean(), axis=0).apply(rfm_func, axis=1)
rfm.head()
```

我们就得到了每个客户的价值：

```python
	F	M	R	label
user_id				
1	1	11.77	545.0	一般挽留客户
2	6	89.00	534.0	一般挽留客户
3	16	156.46	33.0	重要保持客户
4	7	100.50	200.0	一般发展客户
5	29	385.61	178.0	重要保持客户
```

## 用户的生命周期

用户的生命周期就是将用户划分为活跃用户和其他用户。

统计每个用户在所有时间内的消费次数：

```python
data.pivot_table(index='user_id', values='order_dt', aggfunc='count').head()
```

前五条数据为：

```python
	order_dt
user_id	
1	1
2	2
3	6
4	4
5	11
```

统计每个用户每个月的消费次数：

```python
monthly_order = data.pivot_table(index='user_id', values='order_dt', aggfunc='count', columns='order_month', fill_value=0)
monthly_order.head()
```

得到的数据为：

```python
order_month	1997-01-01 00:00:00	1997-02-01 00:00:00	1997-03-01 00:00:00	1997-04-01 00:00:00	1997-05-01 00:00:00	1997-06-01 00:00:00	1997-07-01 00:00:00	1997-08-01 00:00:00	1997-09-01 00:00:00	1997-10-01 00:00:00	1997-11-01 00:00:00	1997-12-01 00:00:00	1998-01-01 00:00:00	1998-02-01 00:00:00	1998-03-01 00:00:00	1998-04-01 00:00:00	1998-05-01 00:00:00	1998-06-01 00:00:00
user_id																		
1	1	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0
2	2	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0
3	1	0	1	1	0	0	0	0	0	0	2	0	0	0	0	0	1	0
4	2	0	0	0	0	0	0	1	0	0	0	1	0	0	0	0	0	0
5	2	1	0	1	1	1	1	0	1	0	0	2	1	0	0	0	0	0
```

统计每个用户每个月是否消费，消费记录为 1 否则记录为 0。

知识点：DataFrame 的 apply 和 applymap 的区别

- applymap：返回 df
- 将函数做用于 DataFrame 中的所有元素（elements）
- apply：返回 Series
- apply() 将一个函数作用于 DataFrame 中的每个行或者列

```python
purchase_status = monthly_order.applymap(lambda x: int(bool(x)))
purchase_status.head()
```

用户当月是否消费的数据为：

```python
order_month	1997-01-01 00:00:00	1997-02-01 00:00:00	1997-03-01 00:00:00	1997-04-01 00:00:00	1997-05-01 00:00:00	1997-06-01 00:00:00	1997-07-01 00:00:00	1997-08-01 00:00:00	1997-09-01 00:00:00	1997-10-01 00:00:00	1997-11-01 00:00:00	1997-12-01 00:00:00	1998-01-01 00:00:00	1998-02-01 00:00:00	1998-03-01 00:00:00	1998-04-01 00:00:00	1998-05-01 00:00:00	1998-06-01 00:00:00
user_id																		
1	1	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0
2	1	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0
3	1	0	1	1	0	0	0	0	0	0	1	0	0	0	0	0	1	0
4	1	0	0	0	0	0	0	1	0	0	0	1	0	0	0	0	0	0
5	1	1	0	1	1	1	1	0	1	0	0	1	1	0	0	0	0	0
```

将用户按照每一个月份分成：

- unreg：观望用户（前两月没买，第三个月才第一次买,则用户前两个月为观望用户）
- unactive：首月购买后，后序月份没有购买则在没有购买的月份中该用户的为非活跃用户
- new：当前月就进行首次购买的用户在当前月为新用户
- active：连续月份购买的用户在这些月中为活跃用户
- return：购买之后间隔 n 月再次购买的第一个月份为该月份的回头客

我们可以使用用户的活跃度算法来计算该月份的用户状态：

```python
# 固定算法
def active_status(data):    # purchase_status表中的一行数据（某一个用户在不同月份的消费行为）
    status = []    # 某个用户每一个月的活跃度
    for i in range(18):
        
        # 若本月没有消费
        if data[i] == 0:
            if len(status) > 0:
                if status[i-1] == 'unreg':
                    status.append('unreg')
                else:
                    status.append('unactive')
            else:
                status.append('unreg')
                    
        # 若本月消费
        else:
            if len(status) == 0:
                status.append('new')
            else:
                if status[i-1] == 'unactive':
                    status.append('return')
                elif status[i-1] == 'unreg':
                    status.append('new')
                else:
                    status.append('active')
    return status
```

将 `purchase_status` 中的原始数据 0 和 1 修改为 new，unactive......，返回新的 df 叫做 `new_purchase_status`：

```python
new_purchase_status = purchase_status.apply(active_status, axis=1)
new_purchase_status.head()
```

我们就得到了用户的活跃状态数据：

```python
order_month	1997-01-01 00:00:00	1997-02-01 00:00:00	1997-03-01 00:00:00	1997-04-01 00:00:00	1997-05-01 00:00:00	1997-06-01 00:00:00	1997-07-01 00:00:00	1997-08-01 00:00:00	1997-09-01 00:00:00	1997-10-01 00:00:00	1997-11-01 00:00:00	1997-12-01 00:00:00	1998-01-01 00:00:00	1998-02-01 00:00:00	1998-03-01 00:00:00	1998-04-01 00:00:00	1998-05-01 00:00:00	1998-06-01 00:00:00
user_id																		
1	new	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive
2	new	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive	unactive
3	new	unactive	return	active	unactive	unactive	unactive	unactive	unactive	unactive	return	unactive	unactive	unactive	unactive	unactive	return	unactive
4	new	unactive	unactive	unactive	unactive	unactive	unactive	return	unactive	unactive	unactive	return	unactive	unactive	unactive	unactive	unactive	unactive
5	new	active	unactive	return	active	active	active	unactive	return	unactive	unactive	return	active	unactive	unactive	unactive	unactive	unactive
```

每月【不同活跃】用户的计数，转置进行最终结果的查看：

```python
new_purchase_status.apply(lambda x: x.value_counts(), axis=0).fillna(0).T
```

最终的每月用户活跃计数：

```python
	active	new	return	unactive	unreg
order_month					
1997-01-01	0.0	7846.0	0.0	0.0	15724.0
1997-02-01	1157.0	8476.0	0.0	6689.0	7248.0
1997-03-01	1681.0	7248.0	595.0	14046.0	0.0
1997-04-01	1773.0	0.0	1049.0	20748.0	0.0
1997-05-01	852.0	0.0	1362.0	21356.0	0.0
1997-06-01	747.0	0.0	1592.0	21231.0	0.0
1997-07-01	746.0	0.0	1434.0	21390.0	0.0
1997-08-01	604.0	0.0	1168.0	21798.0	0.0
1997-09-01	528.0	0.0	1211.0	21831.0	0.0
1997-10-01	532.0	0.0	1307.0	21731.0	0.0
1997-11-01	624.0	0.0	1404.0	21542.0	0.0
1997-12-01	632.0	0.0	1232.0	21706.0	0.0
1998-01-01	512.0	0.0	1025.0	22033.0	0.0
1998-02-01	472.0	0.0	1079.0	22019.0	0.0
1998-03-01	571.0	0.0	1489.0	21510.0	0.0
1998-04-01	518.0	0.0	919.0	22133.0	0.0
1998-05-01	459.0	0.0	1029.0	22082.0	0.0
1998-06-01	446.0	0.0	1060.0	22064.0	0.0
```

