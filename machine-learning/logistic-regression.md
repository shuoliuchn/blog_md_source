## 逻辑斯蒂回归

[TOC]

### 逻辑斯蒂回归概述

我们知道回归模型是用来处理和预测连续型标签的算法。然而逻辑回归，是一种名为“回归”的线性分类器，其本质是由线性回归变化而来的，一种广泛使用于分类问题中的广义回归算法。要理解逻辑回归从何而来，得要先理解线性回归。线性回归是机器学习中最简单的的回归算法，它写作一个几乎人人熟悉的方程（为了更好理解后面的 sigmod 函数，下面的回归函数用 z 来表示）:

![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210945.png)

被统称为模型的参数，其中 θ0 被称为截距(intercept)， θ1~ θn 被称为系数（coefficient）。这个表达式，其实就和我们无比熟悉的 `y = ax + b`。我们可以使用矩阵来表示这个方程，其中 x 和 θ 都可以被看做是一个列矩阵，则有：

![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210942.png)



通过函数 z，线性回归使用输入的特征矩阵 X 来输出一组连续型的标签值 y_pred，以完成各种预测连续型变量的任务（比如预测产品销量，预测股价等等）。那如果我们的标签是离散型变量，尤其是，如果是满足 0-1 分布的离散型变量，我们要怎么办呢？

答案是使用逻辑回归模型。

问题：为什么逻辑回归模型是一个名为回归的分类模型呢？那么回归模型如何实现的分类效果呢？

如果线性回归模型已经训练好了一个模型，可以设置一个阈值：

![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210931.png)

我们可以通过引入 sigmod 函数，将线性回归方程 z 变换为 `g(z)`，并且令 g(z) 的值分布在 `(0,1)` 之间，且当 `g(z)` 接近 0 时样本的标签为类别 0，当 `g(z)` 接近 1 时样本的标签为类别 1，这样就得到了一个分类模型。

e 是自然对数的底数，是一个无限不循环小数，其值是 2.71828...

![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210928.png)

Sigmoid 函数是一个 S 型的函数，当自变量 z 趋近正无穷时，因变量 `g(z)` 趋近于 1，而当 z 趋近负无穷时， `g(z)` 趋近于 0。它能够将任何实数（非 0 和 1 的标签数据）映射到 `(0,1)` 区间，使其可用于将任意值函数转换为更适合二分类的函数。

因为这个性质，Sigmoid 函数也被当作是归一化的一种方法。与我们之前学过的 MinMaxSclaer 同理，是属于数据预处理中的“缩放”功能，可以将数据压缩到 `(0,1)` 之内。区别在于，MinMaxScaler 归一化之后，是可以取到 0 和 1 的（最大值归一化后就是 1，最小值归一化后就是 0），但 Sigmoid 函数只是无限趋近于 0 和 1。

逻辑回归和线性回归之间有无关联呢？

线性回归中 `z = θTx`，于是我们将 z 带入，就得到了二元逻辑回归模型的一般形式：

![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210923.png)

`y(x)` 就是我们逻辑回归返回的标签值。此时 `y(x)` 的取值都在 `(0,1)` 之间，因此 `y(x)` 和 `1-y(x)` 相加必然为 1。如果我们令 `y(x)` 除以 `1 - y(x)` 可以得到形似几率的 `y(x)/1-y(x)`，在此基础上取对数，可以很容易就得到：

![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210920.png)

不难发现，`y(x)` 逻辑回归的形似几率取对数的本质其实就是我们的线性回归 z，我们实际上是在对线性回归模型的预测结果取对数几率来让其的结果无限逼近 0 和 1。因此，其对应的模型被称为”对数几率回归“，也就是我们的逻辑斯蒂回归，这个名为“回归”却是用来做分类工作的分类器。

![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210918.png)

### 逻辑回归的优点

首先必须要声明的一点就是逻辑回归是一个受工业商业热爱，使用广泛的模型。

1. 逻辑回归对线性关系（特征与标签之间的线性关系极强的数据）的拟合效果好到丧心病狂，比如金融领域中的信用卡欺诈，评分卡制作，电商中的营销预测等等相关的数据，都是逻辑回归的强项。相对，逻辑回归在非线性数据中的效果有时候比瞎猜还不如，如果你事先知道你的数据之间的练习是非线性的，千万一定不要使用逻辑回归。
2. 逻辑回归计算快：对于线性数据，逻辑回归的拟合和计算都非常快，计算效率优于 SVM 和随机森林，在大型数据上尤其能看出区别。
3. 逻辑回归返回的分类结果不是固定的 0 和 1，而是以小数形式呈现的类概率数字，我们因此可以把逻辑回归返回的结果当成连续型数据来利用。比如在评分卡制作时，我们不仅需要判断客户是否会违约，还需要给出确定的”信用分“，而这个信用分的计算就需要使用类概率计算出的对数几率（概率） 。

由此，我们已经了解了逻辑回归的本质，它是一个返回对数几率的在线性数据上表现优异的分类器，它主要被应用在金融领域。注意，虽然我们熟悉的逻辑回归通常被用于处理二分类问题，但逻辑回归也可以做多分类。

### 逻辑回归的损失函数

在逻辑回归分类的时候，不管原始样本中的类别使用怎样的值或者文字表示，逻辑回归统一将其视为0类别和1类别。

因为逻辑回归也采用了寻找特征和目标之间的某种关系，则每个特征也是有权重的就是w，那么也会存在真实值和预测值之间的误差（损失函数），那么逻辑回归的损失函数和线性回归的损失函数是否一样呢？

由于逻辑回归是用于分类的，因此该损失函数和线性回归的损失函数是不一样的！逻辑回归采用的损失函数是：对数似然损失函数：

注意：没有求解参数需求的模型是没有损失函数的，比如 KNN，决策树。

损失函数被写作如下：

![lojisidisunshihanshu](logistic-regression.assets/lojisidisunshihanshu.png)

为什么使用 `-log` 函数为损失函数？损失函数的本质就是，如果我们预测对了，则没有损失，反之则损失需要变的很大，而 `-log` 函数在 `(0, 1)` 之间正好符合这一点。

- `-log(h)` 表示分类到正例1的损失

损失函数表征预测值与真实值之间的差异程度，如果预测值与真实值越接近则损失函数应该越小

注意：由于我们追求损失函数的最小值，让模型在训练集上表现最优，可能会引发另一个问题:如果模型在训练集上表示优秀，却在测试集上表现糟糕，模型就会过拟合。所以我们还是需要使用控制过拟合的技术来帮助我们调整模型，对逻辑回归中过拟合的控制，通过正则化来实现。

### 损失函数解释

`yi log(h)` 表示分类到真实标签的正例的损失，根据 `-log` 函数得知如果分类正确则损失值小，反之损失大。

`-(1-yi)log(1-h)` 表示分类到真实标签反例的损失，根据 `-log` 函数得知如果分类正确则损失小，反之损失大。

那么两者相加就获得了逻辑回归模型总分类结果的损失。

将逻辑回归对应的预测结果带入损失函数：![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210856.png)

### 正则化

正则化是用来防止模型过拟合的过程，常用的有 L1 正则化和 L2 正则化两种选项，分别通过在损失函数后加上参数向量 θ 的 L1 范式和 L2 范式的倍数来实现。这个增加的范式，被称为“正则项”，也被称为"惩罚项"。

L1范式表现为参数向量 θ 中的每个参数的绝对值之和：

![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210853.png)

L2 范式表现为参数向量𝛉中的每个参数的平方和的开方值：

![image.png](logistic-regression.assets/Wed,%2022%20Apr%202020%20210852.png)

其中 `J(θ)` 是我们之前提过的损失函数，C 是用来控制正则化程度的超参数，n是方程中特征的总数，也是方程中参数的总数，j 代表每个 θ 参数（w 系数）。在这里，j 要大于等于 1，是因为我们的参数向量中，第一个参数是 θ0，是我们的截距它通常是不参与正则化的。

总结：

- 我们知道损失函数的损失值越小（在训练集中预测值和真实值越接近）则逻辑回归模型就越有可能发生过拟合（模型只在训练集中表现的好，在测试集表现的不好）的现象。通过正则化的 L1 和 L2 范式可以加入惩罚项 C 来矫正模型的拟合度。因为 C 越小则损失函数会越大表示正则化的效力越强，参数 θ 会被逐渐压缩的越来越小。

注意：L1 正则化会将参数 w 压缩为 0，L2 正则化只会让参数尽量小，不会取到 0。

L1 和 L2 范式的区别：
- 在 L1 正则化在逐渐加强的过程中，携带信息量小的、对模型贡献不大的特征的参数 w，会比携带大量信息的、对模型 有巨大贡献的特征的参数更快地变成 0，所以L1正则化本质是一个特征选择的过程。L1 正则化越强，参数向量中就越多的参数为 0，选出来的特征就越少，以此来防止过拟合。因此，如果特征量很大，数据维度很高，我们会倾向于使用 L1 正则化。
- L2 正则化在加强的过程中，会尽量让每个特征对模型都有一些小的贡献，但携带信息少，对模型贡献不大的特征的参数w会非常接近于 0。通常来说，如果我们的主要目的只是为了防止过拟合，选择 L2 正则化就足够了。但是如果选择 L2 正则化后还是过拟合，模型在未知数据集上的效果表现很差，就可以考虑 L1 正则化。

建立两个逻辑回归，L1 正则化和 L2 正则化的差别就一目了然了：

```python
from sklearn.datasets import load_breast_cancer
from sklearn.linear_model import LogisticRegression
# 加载样本数据
cancer = load_breast_cancer()
feature = cancer.data
target = cancer.target
# 建立两种模型
lr1 = LogisticRegression(penalty='l1', C=0.5)
lr2 = LogisticRegression(penalty='l2', C=0.5)
# 逻辑回归的重要属性coef_，查看每个特征所对应的参数
lr1.fit(feature, target)
print('L1范式：', lr1.coef_)
lr2.fit(feature, target)
print('L2范式：', lr2.coef_)
```

输出的结果为：

```python
L1范式： [[ 3.72140975  0.02976337 -0.11003601 -0.01495992  0.          0.
   0.          0.          0.          0.          0.          0.50101851
   0.         -0.07179728  0.          0.          0.          0.
   0.          0.          0.12230699 -0.24312349 -0.13298179 -0.01541796
   0.          0.         -2.15895778  0.          0.          0.        ]]
L2范式： [[ 1.61543234e+00  1.02284415e-01  4.78483684e-02 -4.43927107e-03
  -9.42247882e-02 -3.01420673e-01 -4.56065677e-01 -2.22346063e-01
  -1.35660484e-01 -1.93917198e-02  1.61646580e-02  8.84531037e-01
   1.20301273e-01 -9.47422278e-02 -9.81687769e-03 -2.37399092e-02
  -5.71846204e-02 -2.70190106e-02 -2.77563737e-02  1.98122260e-04
   1.26394730e+00 -3.01762592e-01 -1.72784162e-01 -2.21786411e-02
  -1.73339657e-01 -8.79070550e-01 -1.16325561e+00 -4.27661014e-01
  -4.20612369e-01 -8.69820058e-02]]
```

我们看到，L1 范式的结果很多都是 0，而 L2 则没有。如果我们的特征数很多，可以考虑采用 L1 范式正则化，这样会减少很多特征，缓解计算压力。如果我们特征数不多，可以考虑使用 L2 范式正则化。

L1 范式对过拟合的处理效果要比 L2 范式更加彻底一些。

### 梯度下降

逻辑回归的数学目的是求解能够让模型最优化，拟合程度最好的参数𝛉的值，即求解能够让损失函数 `J(θ)` 最小化的 θ 值。

假设现在有一个带两个特征并且没有截距的逻辑回归 `y(x1, x2)`，两个特征所对应的参数分别为 `(θ1, θ2)`。下面这个华丽的平面就是我们的损失函数  `J(θ1, θ2)` 在以 `(θ1, θ2)` 和 J 为坐标轴的三维立体坐标系上的图像。现在，我们寻求的是损失函数的最小值,也就是图像的最低点。

![5_15_04](logistic-regression.assets/5_15_04-1587573621564.gif)

那我们怎么做呢?我在这个图像上随机放一个小球，当我松手，这个小球就会顺着这个华丽的平面滚落，直到滚到 紫色的区域——损失函数的最低点。为了严格监控这个小球的行为，我让小球每次滚动的距离有限，不让他一次 性滚到最低点，并且最多只允许它滚动 100 步，还要记下它每次滚动的方向，直到它滚到图像上的最低点。

可以看见，小球从高处滑落，在紫色的区域中来回震荡，最终停留在了图像凹陷处的某个点上。非常明显，我们可以观察到几个现象:

首先，小球并不是一开始就直向着最低点去的，它先一口气冲到了紫色区域边缘，后来又折回来，我们已经规定了小球是多次滚动，所以可见，小球每次滚动的方向都是不同的。

另外，小球在进入紫色区域后，并没有直接找到某个点，而是在紫色区域中来回震荡了数次才停下。这有两种可能：

1. 小球已经滚到了图像的最低点，所以停下了，
2. 由于我设定的步数限制，小球还没有找到最低点，但也只 好在 100 步的时候停下了。也就是说，小球不一定滚到了图像的最低处。

但无论如何，小球停下的就是我们在现有状况下可以获得的唯一点了。如果我们够幸运，这个点就是图像的最低点，那我们只要找到这个点的对应坐标 `(θ1, θ2, J)`，就可以获取能够让损失函数最小的参数取值 `(θ1,θ2)` 了。如此，梯度下降的过程就已经完成。

在这个过程中，小球其实就是一组组的坐标点 `(θ1, θ2, J)`。小球每次滚动的方向就是那一个坐标点的梯度向量的方 向，因为每滚动一步，小球所在的位置都发生变化，坐标点和坐标点对应的梯度向量都发生了变化，所以每次滚动的方向也都不一样。人为设置的 100 次滚动限制，就是 sklearn 中逻辑回归的参数 max_iter，代表着能走的最大步数.

所以梯度下降，其实就是在众多 `(θ1, θ2)` 可能的值中遍历，一次次求解坐标点的梯度向量,不断让损失函数的取值J逐渐逼近最小值，再返回这个最小值对应的参数取值 `(θ1, θ2)` 的过程。

### 逻辑回归 API

```python
from sklearn.linear_model import LogisticRegression
```

超参数介绍：

- penalty：可以输入 l1 或者 l2 来指定使用哪一种正则化方式。不填写默认"l2"。 注意，若选择"l1"正则化，参数 solver 仅能够使用求解方式”liblinear"和"saga“，若使用“l2”正则 化，参数 solver 中所有的求解方式都可以使用。
- C：惩罚项。必须是一个大于 0 的浮点数，不填写默认 1.0，即默认正则项与损失函数的比值是 1:1。C 越小，损失函数会越大，模型对损失函数的惩罚越重，正则化的效力越强，参数会逐渐被压缩得越来越小。
- max_iter：梯度下降中能走的最大步数，默认值为 100。步数的不同取值可以帮助我们获取不同的损失函数的损失值。目前没有好的办法可以计算出最优的 max_iter 的值，一般是通过绘制学习曲线对其进行取值。
- solver：我们之前提到的梯度下降法，只是求解逻辑回归参数 θ 的一种方法。sklearn 为我们提供了多种选择，让我们可以使用不同的求解器来计算逻辑回归。求解器的选择，由参数"solver"控制，共有五种选择。
  - liblinear：是二分类专用，也是现在的默认求解器。
  - lbfgs、newton-cg、sag、saga：是多分类专用，几乎不用。
- multi_class：
  - 输入"ovr"，"multinomial"，"auto"来告知模型，我们要处理的分类问题的类型。默认是"ovr"。
    - 'ovr'：表示分类问题是二分类，或让模型使用"一对多"的形式来处理多分类问题。
    - 'multinomial'：表示处理多分类问题，这种输入在参数solver是'liblinear'时不可用。
    - "auto"：表示会根据数据的分类情况和其他参数来确定模型要处理的分类问题的类型。比如说，如果数据是二分 类，或者solver的取值为"liblinear"，"auto"会默认选择"ovr"。反之，则会选择"multinomial"。
- class_weight:
  - 表示样本不平衡处理的参数。样本不平衡指的是在一组数据中，某一类标签天生占有很大的比例，或误分类的代价很高，即我们想要捕捉出某种特定的分类的时候的状况。什么情况下误分类的代价很高?
    - 例如，我们现在要对潜在犯罪者和普通人进行分类，如果没有能够识别出潜在犯罪者，那么这些人就可能去危害社会，造成犯罪，识别失败的代价会非常高，但如果，我们将普通人错误地识别成了潜在犯罪者，代价却相对较小。所以我们宁愿将普通人分类为潜在犯罪者后再人工甄别，但是却不愿将潜在犯罪者 分类为普通人，有种"宁愿错杀不能放过"的感觉。
    - 再比如说，在银行要判断“一个新客户是否会违约”，通常不违约的人vs违约的人会是 99:1 的比例，真正违约的人其实是非常少的。这种分类状况下，即便模型什么也不做，全把所有人都当成不会违约的人，正确率也能有 99%， 这使得模型评估指标变得毫无意义，根本无法达到我们的“要识别出会违约的人”的建模目的。
  - None：我们要使用参数 class_weight 对样本标签进行一定的均衡，给少量的标签更多的权重，让模型更偏向少数类， 向捕获少数类的方向建模。该参数默认 None，此模式表示自动给与数据集中的所有标签相同的权重，即自动 1: 1。
  - balanced：当误分类的代价很高的时候，我们使用”balanced“模式，我们只是希望对标签进行均衡的时候，什么都不填就 可以解决样本不均衡问题。

接下来，我们通过实例，找到乳腺癌数据集下，max_iter 的学习曲线：

```python
import matplotlib.pyplot as plt
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score
# 加载数据
cancer = load_breast_cancer()
feature = cancer.data
target = cancer.target
# 拆分数据
x_train, x_test, y_train, y_test = train_test_split(feature, target, random_state=420, test_size=0.3)
# 训练并测试模型，收集预测效果
l2_test = []
l2_train = []
for i in range(1, 202, 10):
    lr = LogisticRegression(penalty='l2', C=0.9, max_iter=i)
    lr.fit(x_train, y_train)
    l2_train.append(accuracy_score(lr.predict(x_train), y_train))
    l2_test.append(accuracy_score(lr.predict(x_test), y_test))
# 绘制学习曲线
graph = [l2_train, l2_test]
color = ['black', 'red']
label = ['L2 Train', 'L2 Test']
plt.figure(figsize=(20, 5))
for i in range(len(graph)):
    plt.plot(range(1, 202, 10), graph[i], color[i], label=label)
plt.legend(loc=4)
plt.xticks(range(1, 202, 10))
plt.show()
```

得到的学习曲线为：

![img](logistic-regression.assets/Thu,%2023%20Apr%202020%20010718.png)

我们看到，训练数据要比测试数据的拟合度高。这时理所当然的，因为毕竟模型就是用训练集训练出来的。预测结果最好的 max_iter 取值应该在 21 附近。我们可以使用这个值来对模型进行预测了。

