## 2012 美国大选献金项目数据分析

众所周知，美国大选是一个非常耗钱的事情。每个总统参加竞选时，都需要大量的经费。这些经费，几乎不会由候选人自己出，它们往往来自于政治献金。

美国法律要求，政治献金数据必须公开透明。于是，我们有了可以分析的数据。

需求：

- 加载数据
- 查看数据的基本信息
- 指定数据截取，将如下字段的数据进行提取，其他数据舍弃
  - cand_nm：候选人姓名
  - contbr_nm：捐赠人姓名
  - contbr_st：捐赠人所在州
  - contbr_employer：捐赠人所在公司
  - contbr_occupation：捐赠人职业
  - contb_receipt_amt：捐赠数额（美元）
  - contb_receipt_dt：捐款的日期
- 对新数据进行总览，查看是否存在缺失数据
- 用统计学指标快速描述数值型属性的概要
- 空值处理。可能因为忘记填写或者保密等等原因，相关字段出现了空值，将其填充为 NOT PROVIDE
- 异常值处理。将捐款金额 <= 0 的数据删除
- 新建一列为各个候选人所在党派 party
- 查看 party 这一列中有哪些不同的元素
- 统计 party 列中各个元素出现次数
- 查看各个党派收到的政治献金总数 contb_receipt_amt
- 查看具体每天各个党派收到的政治献金总数 contb_receipt_amt
- 将表中日期格式转换为 `'yyyy-mm-dd'`
- 查看老兵（捐献者职业）DISABLED VETERAN 主要支持谁

在开始之前，导入各种需要的模块：

```python
import numpy as np
import pandas as pd
from pandas import Series, DataFrame
```

加载数据。数据存储在文本文件中，直接用 read_csv 读取即可：

```python
df = pd.read_csv('data/usa_election.txt')
df.head()
```

前五条数据为：

```python
	cmte_id	cand_id	cand_nm	contbr_nm	contbr_city	contbr_st	contbr_zip	contbr_employer	contbr_occupation	contb_receipt_amt	contb_receipt_dt	receipt_desc	memo_cd	memo_text	form_tp	file_num
0	C00410118	P20002978	Bachmann, Michelle	HARVEY, WILLIAM	MOBILE	AL	3.6601e+08	RETIRED	RETIRED	250.0	20-JUN-11	NaN	NaN	NaN	SA17A	736166
1	C00410118	P20002978	Bachmann, Michelle	HARVEY, WILLIAM	MOBILE	AL	3.6601e+08	RETIRED	RETIRED	50.0	23-JUN-11	NaN	NaN	NaN	SA17A	736166
2	C00410118	P20002978	Bachmann, Michelle	SMITH, LANIER	LANETT	AL	3.68633e+08	INFORMATION REQUESTED	INFORMATION REQUESTED	250.0	05-JUL-11	NaN	NaN	NaN	SA17A	749073
3	C00410118	P20002978	Bachmann, Michelle	BLEVINS, DARONDA	PIGGOTT	AR	7.24548e+08	NONE	RETIRED	250.0	01-AUG-11	NaN	NaN	NaN	SA17A	749073
4	C00410118	P20002978	Bachmann, Michelle	WARDENBURG, HAROLD	HOT SPRINGS NATION	AR	7.19016e+08	NONE	RETIRED	300.0	20-JUN-11	NaN	NaN	NaN	SA17A	736166
```

查看数据的基本信息，调用 info 即可搞定：

```python
df.info()
```

数据信息拿到。我们可以分析出来，总共有 536041 条数据，有些列是含有空值的：

```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 536041 entries, 0 to 536040
Data columns (total 16 columns):
cmte_id              536041 non-null object
cand_id              536041 non-null object
cand_nm              536041 non-null object
contbr_nm            536041 non-null object
contbr_city          536026 non-null object
contbr_st            536040 non-null object
contbr_zip           535973 non-null object
contbr_employer      525088 non-null object
contbr_occupation    530520 non-null object
contb_receipt_amt    536041 non-null float64
contb_receipt_dt     536041 non-null object
receipt_desc         8479 non-null object
memo_cd              49718 non-null object
memo_text            52740 non-null object
form_tp              536041 non-null object
file_num             536041 non-null int64
dtypes: float64(1), int64(1), object(14)
memory usage: 65.4+ MB
```

指定数据截取，将指定字段的数据进行提取，其他数据舍弃。注意提取数据的时候，内部是一个列表：

```python
data = df[['cand_nm', 'contbr_nm', 'contbr_st', 'contbr_employer', 'contbr_occupation', 'contb_receipt_amt', 'contb_receipt_dt']]
data.head()
```

截取后的前五条数据为：

```python
	cand_nm	contbr_nm	contbr_st	contbr_employer	contbr_occupation	contb_receipt_amt	contb_receipt_dt
0	Bachmann, Michelle	HARVEY, WILLIAM	AL	RETIRED	RETIRED	250.0	20-JUN-11
1	Bachmann, Michelle	HARVEY, WILLIAM	AL	RETIRED	RETIRED	50.0	23-JUN-11
2	Bachmann, Michelle	SMITH, LANIER	AL	INFORMATION REQUESTED	INFORMATION REQUESTED	250.0	05-JUL-11
3	Bachmann, Michelle	BLEVINS, DARONDA	AR	NONE	RETIRED	250.0	01-AUG-11
4	Bachmann, Michelle	WARDENBURG, HAROLD	AR	NONE	RETIRED	300.0	20-JUN-11
```

对新数据进行总览，查看是否存在缺失数据

```python
data.info()
```

除了捐助者和被捐助者，其余列都有空值存在：

```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 536041 entries, 0 to 536040
Data columns (total 7 columns):
cand_nm              536041 non-null object
contbr_nm            536041 non-null object
contbr_st            536040 non-null object
contbr_employer      525088 non-null object
contbr_occupation    530520 non-null object
contb_receipt_amt    536041 non-null float64
contb_receipt_dt     536041 non-null object
dtypes: float64(1), object(6)
memory usage: 28.6+ MB
```

用统计学指标快速描述数值型属性的概要

```python
data.describe()
```

因为只有献金金额是数值型数据，所以结果中只有现金金额这一列：

```python
	contb_receipt_amt
count	5.360410e+05
mean	3.750373e+02
std	3.564436e+03
min	-3.080000e+04
25%	5.000000e+01
50%	1.000000e+02
75%	2.500000e+02
max	1.944042e+06
```

空值处理。可能因为忘记填写或者保密等等原因，相关字段出现了空值，将其填充为 NOT PROVIDE

```python
data.fillna('NOT PROVIDE', inplace=True)
data.info()
```

然后就可以看到，数据中已经没有空值了：

```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 536041 entries, 0 to 536040
Data columns (total 7 columns):
cand_nm              536041 non-null object
contbr_nm            536041 non-null object
contbr_st            536041 non-null object
contbr_employer      536041 non-null object
contbr_occupation    536041 non-null object
contb_receipt_amt    536041 non-null float64
contb_receipt_dt     536041 non-null object
dtypes: float64(1), object(6)
memory usage: 28.6+ MB
```

异常值处理。将捐款金额 <= 0 的数据删除：

```python
data = data.loc[data['contb_receipt_amt'] > 0]
data.describe()
```

从统计数据中可以看出，最小值也是大于 0 的：

```python
	contb_receipt_amt
count	5.303140e+05
mean	3.931043e+02
std	3.576869e+03
min	1.000000e-02
25%	5.000000e+01
50%	1.000000e+02
75%	2.500000e+02
max	1.944042e+06
```

新建一列为各个候选人所在党派 party，使用映射可以实现：

```python
dic = {
    'Bachmann, Michelle': 'Republican',
    'Romney, Mitt': 'Republican',
    'Obama, Barack': 'Democrat',
    "Roemer, Charles E. 'Buddy' III": 'Reform',
    'Pawlenty, Timothy': 'Republican',
    'Johnson, Gary Earl': 'Libertarian',
    'Paul, Ron': 'Republican',
    'Santorum, Rick': 'Republican',
    'Cain, Herman': 'Republican',
    'Gingrich, Newt': 'Republican',
    'McCotter, Thaddeus G': 'Republican',
    'Huntsman, Jon': 'Republican',
    'Perry, Rick': 'Republican',
}
data['party'] = data['cand_nm'].map(dic)
data.head()
```

数据中的前五行为：

```python
	cand_nm	contbr_nm	contbr_st	contbr_employer	contbr_occupation	contb_receipt_amt	contb_receipt_dt	party
0	Bachmann, Michelle	HARVEY, WILLIAM	AL	RETIRED	RETIRED	250.0	20-JUN-11	Republican
1	Bachmann, Michelle	HARVEY, WILLIAM	AL	RETIRED	RETIRED	50.0	23-JUN-11	Republican
2	Bachmann, Michelle	SMITH, LANIER	AL	INFORMATION REQUESTED	INFORMATION REQUESTED	250.0	05-JUL-11	Republican
3	Bachmann, Michelle	BLEVINS, DARONDA	AR	NONE	RETIRED	250.0	01-AUG-11	Republican
4	Bachmann, Michelle	WARDENBURG, HAROLD	AR	NONE	RETIRED	300.0	20-JUN-11	Republican
```

查看 party 这一列中有哪些不同的元素

```python
data['party'].unique()
```

候选人来自的党派有：

```python
array(['Republican', 'Democrat', 'Reform', 'Libertarian'], dtype=object)
```

统计 party 列中各个元素出现次数。一般情况下，只有能够接收到足够献金的党派胜选的概率才会更大：

```python
data['party'].value_counts()
```

毫无疑问，民主和共和两党收到的献金是最多的：

```python
Democrat       289999
Republican     234300
Reform           5313
Libertarian       702
Name: party, dtype: int64
```

查看各个党派收到的政治献金总数 contb_receipt_amt

```python
data.groupby('party')['contb_receipt_amt'].sum()
```

各党派收到献金的总数为：

```python
party
Democrat       8.259441e+07
Libertarian    4.132769e+05
Reform         3.429658e+05
Republican     1.251181e+08
Name: contb_receipt_amt, dtype: float64
```

查看具体每天各个党派收到的政治献金总数 contb_receipt_amt

```python
data.groupby(by = ['contb_receipt_dt', 'party'])['contb_receipt_amt'].sum()
```

各党派每天收到的献金总数为：

```python
contb_receipt_dt  party      
01-APR-11         Reform              50.00
                  Republican       12635.00
01-AUG-11         Democrat        182198.00
                  Libertarian       1000.00
                  Reform            1847.00
                  Republican      268903.02
01-DEC-11         Democrat        651982.82
                  Libertarian        725.00
                  Reform             875.00
                  Republican      505255.96
01-FEB-11         Republican         250.00
01-JAN-11         Republican        8600.00
01-JAN-12         Democrat         74303.80
                  Reform             515.00
                  Republican       76804.72
01-JUL-11         Democrat        175364.00
                  Libertarian       2000.00
                  Reform             100.00
                  Republican      125973.72
01-JUN-11         Democrat        148409.00
                  Libertarian        500.00
                  Reform              50.00
                  Republican      435609.20
01-MAR-11         Republican        1000.00
01-MAY-11         Democrat         82644.00
                  Reform             480.00
                  Republican       28663.87
01-NOV-11         Democrat        129309.87
                  Libertarian       3000.00
                  Reform            1792.00
                                    ...    
30-OCT-11         Reform            3910.00
                  Republican       46413.16
30-SEP-11         Democrat       3409587.24
                  Libertarian        550.00
                  Reform            2050.00
                  Republican     5094824.20
31-AUG-11         Democrat        375487.44
                  Libertarian      10750.00
                  Reform             450.00
                  Republican     1038330.90
31-DEC-11         Democrat       3571793.57
                  Reform             695.00
                  Republican     1165777.72
31-JAN-11         Republican        6000.00
31-JAN-12         Democrat       1421887.31
                  Reform             150.00
                  Republican      963681.41
31-JUL-11         Democrat         20305.00
                  Reform            1066.00
                  Republican       12781.02
31-MAR-11         Reform             200.00
                  Republican       74575.00
31-MAY-11         Democrat        352005.66
                  Libertarian        250.00
                  Reform             100.00
                  Republican      313839.80
31-OCT-11         Democrat        216971.87
                  Libertarian       4250.00
                  Reform            3205.00
                  Republican      751542.36
Name: contb_receipt_amt, Length: 1183, dtype: float64
```

将表中日期格式转换为 `'yyyy-mm-dd'`，可通过运算工具实现：

```python
months = {'JAN': 1, 'FEB': 2, 'MAR': 3, 'APR': 4, 'MAY': 5, 'JUN': 6,
          'JUL': 7, 'AUG': 8, 'SEP': 9, 'OCT': 10, 'NOV': 11, 'DEC': 12}
def transfer_date(d):
    day, month, year = d.split('-')
    month = months[month]
    return '20%s-%02d-%2s' % (year, month, day)
data['contb_receipt_dt'] = data['contb_receipt_dt'].map(transfer_date)
data.head()
```

数据的前五行为：

```python
	cand_nm	contbr_nm	contbr_st	contbr_employer	contbr_occupation	contb_receipt_amt	contb_receipt_dt	party
0	Bachmann, Michelle	HARVEY, WILLIAM	AL	RETIRED	RETIRED	250.0	2011-06-20	Republican
1	Bachmann, Michelle	HARVEY, WILLIAM	AL	RETIRED	RETIRED	50.0	2011-06-23	Republican
2	Bachmann, Michelle	SMITH, LANIER	AL	INFORMATION REQUESTED	INFORMATION REQUESTED	250.0	2011-07-05	Republican
3	Bachmann, Michelle	BLEVINS, DARONDA	AR	NONE	RETIRED	250.0	2011-08-01	Republican
4	Bachmann, Michelle	WARDENBURG, HAROLD	AR	NONE	RETIRED	300.0	2011-06-20	Republican
```

查看老兵（捐献者职业）DISABLED VETERAN 主要支持谁。

我们认为，哪个候选人接收到老兵的献金越多，老兵就越支持谁。

运算分两步，首先提取出老兵对应的行数据，然后根据候选人分组，运算得到老兵给每个候选人的献金：

```python
old_soldier = data.loc[data['contbr_occupation'] == 'DISABLED VETERAN']
old_soldier.groupby('cand_nm').groups
```

我们可以看到有哪些老兵给哪位候选人献金：

```python
{'Cain, Herman': Int64Index([459440, 459447, 464508], dtype='int64'),
 'Obama, Barack': Int64Index([149790, 150910, 174041, 175244, 183790, 184211, 184417, 184761,
             185140, 185264, 186026, 186361, 194750, 205094, 210809, 210810,
             210811, 225948, 225954, 231074, 231773, 232434, 233434, 235014,
             235112, 235135, 235846, 244060, 260154, 260544, 264154, 289927],
            dtype='int64'),
 'Paul, Ron': Int64Index([379163, 379537, 379555, 396211, 408043, 408743, 419163, 420332,
             420517, 421427, 421429, 421535, 421621, 421741, 421754, 421839,
             421895, 429191, 430649, 431049, 435011, 446880],
            dtype='int64'),
 'Santorum, Rick': Int64Index([453659, 453660, 453661], dtype='int64')}
```

对分组数据中的献金金额求和，即可得到每位候选人收到老兵献金的总额：

```python
old_soldier.groupby('cand_nm')['contb_receipt_amt'].sum()
```

可见，奥巴马是最受老兵支持的：

```python
cand_nm
Cain, Herman       300.00
Obama, Barack     4205.00
Paul, Ron         2425.49
Santorum, Rick     250.00
Name: contb_receipt_amt, dtype: float64
```