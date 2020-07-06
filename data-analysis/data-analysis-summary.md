# 数据分析总结

总结一下数据分析三剑客（NumPy、Pandas 和 Matplotlib）的常用方法。

## numpy

### 数组的创建形式

- `np.array(data=列表)`
- `plt.imread(图片地址)`
- `np.ones()`
- `np.linspace()`：创建一个一维数组，指定范围和元素个数
- `np.arange()`：创建一个一维数组，指定范围和步长
- `np.random.randint()`：创建任意维度的随机数组

### 常用的属性

- shape：数组的形状
- ndim：数组的维度
- size：数组元素的个数
- dtype：数组元素的数据类型

### 修改数组数据类型

- `arr.dtype=赋值`
- `astype(数据类型)`

### numpy 数组主要是用来存储和运算数值型的数据

- int，float

### numpy 数组的索引

- 索引取行：`arr[行索引]`
- 索引取列：`arr[:, 列索引]`
- 索引取元素：`arr[行索引:列索引]`

### numpy 数组的切片

- 切行：`arr[行切片]`
- 切列：`arr[:, 列切片]`

### 级联：concatnate

- 将多个 numpy 数组进行横向或者纵向的拼接

### 变形

- `reshape()` 将任意维度的数组进行任意维度的变形

### 基于切片操作翻转图片

- `arr[::-1,::-1]`
- 基于切片裁剪图片

### 聚合函数

- max，min，mean，min

### 统计函数

- std：标准差
- var：方差

### 矩阵操作

- 矩阵转置：`.T`
  - 将行变成列，列变成行
- 矩阵乘法：`np.dot`

## pandas

两个常用的类

- Series，DataFrame

### Series（一维的数据结构）

#### 创建形式

- `Series(data=[1,2,3])`
- `Series(data=np数组)`
- `Series(data=字典)`

#### isnull，notnull

检测 Series 的元素是否为空或者是否为非空

#### `unique()`

对 Series 元素去重

#### `nunique()`

统计 Series 去重后的元素个数

#### Series 的运算法则

- 如果两个 Series 之间进行算数运算的话，则只可以对索引一致的元素进行算数运算，否则补空。

#### 显式索引

可以通过 index 设置 Series 的显式索引，显式索引不会覆盖默认的隐式索引。

### DataFrame（二维的数据结构）

#### 创建方式

- DataFrame(data=二维列表)
- DataFrame(data=二维的numpy数组)
- DataFrame(data=字典)

#### 显式索引

index，columns 设定 df 的显式索引

#### 索引操作

- 索引取单列：`df[列索引]`
- 索引取多列：`df[[col1,col2,col3]]`
- 索引取单行：`df.iloc[行索引]`
- 索引取多行：`df.iloc[[index1,index2]]`
- `iloc[隐式索引]`，`loc[显示索引]`
- 索引取元素：`df.iloc[行索引，列索引]`

#### 切片操作

- 切行：`df[行切片]`
- 切列：`df.iloc[:, 列切片]`

#### 时间类型的转换

- `pd.to_datetime(df['date'])`

#### 列转换为行索引

- `set_index(df['col'])`

#### tushare 模块

财经数据接口包

#### df 的持久化存储

- `df.to_xxx()`

#### df 数据的加载

- `pd.read_xxx()`

#### 指定数据的删除

- `drop()`

#### 将 Series 的元素整体上移或者下移

- `shift(1)`

#### 布尔值索引

可以直接将布尔值作为df源数据的行索引，可以取出 True 对应的行数据

#### 数据的重新取样

- `resample('M').fist()`

#### 5 日均值的计算

- `rolling(5).mean()`

#### 排序

`sort_index()`：根据索引排序

`soert_value()`：根据值排序

#### df 的级联

- `concat()`

- 匹配级联 & 不匹配级联

df 的合并

- `merge()`

- how：inner，outer，left，right

#### df 的运算法则

- 同Series

#### df 的条件查询

- `query()`

#### df 的替换操作

- `replace()`

- 单值替换
- 多值替换
- 指定列替换

#### 映射

- `map(字典形式的映射关系表)`

#### Series 的运算工具

- `map(func)`

#### 随机抽样

- `take()`：将 df 的行列打乱
- `random.permutation(n)`：0 到 n - 1 之间的乱序的随机序列

#### 运算工具

apply：d f的运算工具，是对 df 的行或者列进行某种形式的运算

applymap：df 的运算工具，是对 df 的每一个元素进行某种形式的运算

#### 数据清洗

- 缺失值
  - any，all，dropna，fillna
- 重复值
  - `drop_duplicates(keep)`
- 异常值
  - 基于异常值判定的条件返回的布尔值去除异常值对应的行数据

#### 分组操作

- `groupby()`

#### 分组聚合

- `agg(['max','mean'])`：分组后实现多种不同形式的聚合操作

#### 高级聚合

- 自定义分组后的聚合函数
- `apply(func)`，`transform(func)`

#### 透视表

- 参数：
  - index
  - value
  - aggfunc
  - columns

#### 交叉表

- 了解即可

#### axis 轴向

- 在 drop，dropna，take 三个函数中，axis=0 表示行，axis=1 表示的是列
- 在其他函数中 axis=0 是列，axis=1 是行

## matplotlib

- 线形图：`plt.plot()`
- 柱状图：`plt.bar()`
- 直方图：`plt.hist()`
- 饼图：`plt.pie()`
- 散点图：`plt.scatter()`