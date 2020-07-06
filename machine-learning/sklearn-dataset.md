## sklearn 的数据集

[TOC]

### 数据集划分

前提：机器学习就是从数据中自动分析获得规律，并利用规律对未知数据进行预测。换句话说，我们的模型一定是要经过样本数据对其进行训练，才可以对未知数据进行预测的。

问题：我们得到数据后，是否将数据全部用来训练模型呢？

当然不是！因为我们如果模型（数据的规律）都是从数据中得来的，那么该模型的性能评估如何进行呢？还是基于对原先的数据进行预测吗？可想不是的，如果模型对原先的数据进行预测，由于模型（数据的规律）本来就是从该数据中获取的，所以预测的精度几乎会是百分之百。所以想要评估模型的好坏，需要使用一组新数据对模型进行评估。

因此我们往往需要将原先的样本数据拆分成两部分：

- 训练集：训练模型
- 测试集：评估模型
  - 不同类型的模型对应的评估方式是不一样的

数据集划分的 API

- `from sklearn.model_selection import train_test_split`
- `train_test_split(x,y,test_size,random_state)` 参数介绍：
  - x：特征
  - y：目标
  - test_size：测试集的比例
  - random_state：打乱的随机种子
- 返回值：训练特征，测试特征，训练目标，测试目标

### 数据集接口

- `sklearn.datasets.load_*()`：获取小规模的数据集
- `sklearn.datasets.fetch_*(data_home=None,subset)`：获取大规模的数据集 data_home 表示数据集下载目录，None 为默认值表示的是家目录 `/scikit_learn_data`（自动创建该文件夹）下。需要从网络下载 `.subset` 为需要下载的数据集，可以为 train，test，all

### 示例

首先获取鸢尾花的数据，通过 load 方法：

```python
from sklearn import datasets
data = datasets.load_iris()
data
```

返回的结果为一个字典，鸢尾花的特征数据存放在 data 中，目标数据存放在 target 中：

```python 
{'DESCR': 'Iris Plants Database\n ...\n',
 'data': array([[5.1, 3.5, 1.4, 0.2],
        [4.9, 3. , 1.4, 0.2],
        ...
        [5.9, 3. , 5.1, 1.8]]),
 'feature_names': ['sepal length (cm)',
  'sepal width (cm)',
  'petal length (cm)',
  'petal width (cm)'],
 'target': array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
        0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
        0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
        1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
        1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,
        2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,
        2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2]),
 'target_names': array(['setosa', 'versicolor', 'virginica'], dtype='<U10')}
```

除了使用 load 获取较少数据集之外，我们还可以通过 fetch 方法获取大量数据：

```python
from sklearn import datasets
data = datasets.fetch_20newsgroups()
data
```

因为数据量比较大，且服务器在国外，所以可能会花费一点时间。因为数据很多很乱，就不放在这里了。总之数据结构跟上面差不多。

下面，我们将鸢尾花数据集进行拆分：

```python
# 获取数据集
from sklearn import datasets
from sklearn.model_selection import train_test_split
data = datasets.load_iris()
# 提取特征数据和目标数据
feature = data['data']    # 特征数据
target = data['target']    # 目标数据
# 拆分数据集train_test_split(x,y,test_size,random_state)
x_train, x_test, y_train, y_test = train_test_split(feature, target, test_size=0.2, random_state=10)
```

`train_test_split(x,y,test_size,random_state)` 参数及返回值解释：

- x：特征数据，feature
- y：目标数据，target
- test_size：测试数据占总数据的必重
- random_state：随机状态参数。默认值为 None。如果不设置或设置为 None，将会随机抽取样品，且每次运行获取到的样品几乎都是不同的。将其设置为任意数字，仍然随机取样，但是每次运行提取到的样品相同。
- x_train：训练集的特征数据
- x_test：测试集的特征数据
- y_train：训练集的目标数据
- y_test：测试集的目标数据