## seaborn 基本用法

除了 Matplotlib 之外，seaborn 也是一个用来绘制图像，实现数据可视化的工具。Matplotlib 试着让简单的事情更加简单，困难的事情变得可能，而 seaborn 就是让困难的东西更加简单。

用 Matplotlib 最大的困难是其默认的各种参数，而 seaborn 则完全避免了这一问题。

seaborn 是针对统计绘图的，一般来说，seaborn 能满足数据分析 90% 的绘图需求，够用了。如果需要复杂的自定义图形，还是要使用 Matplotlib。

在开始之前，导入模块：

```python
import seaborn as sns
import numpy as np
```

### 直方图：单变量单特征数据分析图

seaborn 的直方图的画法和 matplotlib 是很类似的，使用的方法是 distplot：

```python
arr = np.array([1, 1, 2, 2, 3, 3, 3, 3, 4, 5, 6, 6, 7, 8, 9, 9, 9, 9, 9, 10])
sns.distplot(arr, bins=15)
```

seaborn 生成的图像要更高级美观一些：

![img](seaborn-abc.assets/Tue,%2007%20Apr%202020%20013728.png)

### 散点图：观测两个变量之间的分布关系最好的图之一

seaborn 绘制散点图使用的方法是 jointplot，余下的操作就很简单了：

```python
x = np.random.random(size=(50,))
y = np.random.random(size=(50,))
sns.jointplot(x, y)
```

生成的散点图同样很高级：

![img](seaborn-abc.assets/Tue,%2007%20Apr%202020%20014049.png)

我们还可以通过指定 kind 参数，生成蜂窝风格的散点图：

```python
x = np.random.random(size=(50,))
y = np.random.random(size=(50,))
sns.jointplot(x, y, kind='hex')
```

图像为：

![img](seaborn-abc.assets/Tue,%2007%20Apr%202020%20014200.png)