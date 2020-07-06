# Matplotlib 绘图基础

我们使用 Pandas 千辛万苦处理出来一些数据，却发现，这些数据仍然很抽象。如果没有参与运算，或者对数字不是那么敏感的话，可能一时找不到这里面的规律。这时，我们可以考虑把数据绘制成图表，让其仍容易理解，一目了然。

Matplotlib 用来进行数据的可视化处理，也就是把抽象的数据转换成图像展示出来。

最开始，我们还是要导入各种模块：

```python
import matplotlib.pyplot as plt
import numpy as np

#jupyter中的魔法指令，相当于为我们节约了一行 plt.show代码
#如果绘制的图像不显示，则执行该操作
%matplotlib inline
```

## plt.plot 绘制线性图

线型图是最基本的曲线图了，适用于把规律很明显的数据展现出来

### 绘制单条线形图

单个直线的绘制

首先，我们需要两组数据，然后对这两组数据绘图：

```python
x = np.linspace(-np.pi, np.pi, num=10)
y = x * 2 + 3.1
plt.plot(x, y)
```

这是一个线性函数，得到的图像当然是一条直线：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20002807.png)

我们也可以绘制二次函数的曲线：

```python
x = x
y = x ** 2
plt.plot(x, y)
```

得到的曲线就是一个抛物线，因为选的点比较少，所以看起来棱角分明：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20002951.png)

### 绘制多条线形图

我们可以通过指定多组 xy，绘制多条线形图：

```python
x = x
y = 1 - x ** 2

x1 = x - 1
y1 = x1 ** 2

plt.plot(x, y, x1, y1)
```

就得到两条曲线：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20003303.png)

我们也可以通过连续多次调用 plot 实现绘制多条曲线：

```python
plt.plot(x, y)
plt.plot(x1, y1)
```

结果是一样的：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20003549.png)

### 设置坐标系的比例

在绘图之前配置 plt.figure(figsize=(a,b))，即可设置坐标系的比例。将坐标系等比例放大或者缩小，刻度是不会改变的：

```python
plt.figure(figsize=(10, 10))
plt.plot(x, y)
plt.plot(x1, y1)
```

我们发现，只是比例变了，也就是图像大小变了，但是坐标范围是不变的：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20003824.png)

### 设置图例

在每个 plot 方法中，通过指定 label 参数设置图例的名称，最后要使用 legend 方法应用图例，显示图像（注意，如果不在最后使用 legend 方法，图例是显示不出来的）：

```python
plt.plot(x, y, label='temp')
plt.plot(x1, y1, label='dist')
plt.legend()
```

显示图例的图像为：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20004154.png)

### 设置轴的标识

我们可以通过设置 xlabel 和 ylabel 属性，指定 x 轴和 y 轴的标识，也可以通过 title 指定标题：

```python
plt.plot(x, y, label='temp')
plt.plot(x1, y1, label='dist')
plt.xlabel('city_tmp')
plt.ylabel('city_dist')
plt.title('temp&dist')
plt.legend()
```

指定了轴标识和标题的图像为：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20004453.png)

### 图例保存

生成的图像有时候需要保存起来，在其他地方使用。图像保存只需要三步即可：

1. `fig = plt.figure()`，实例化 figure 对象
2. `plt.plot(x, y)`，载入图像信息
3. `fig.savefig()`，保存图像

用代码表示就是：

```python
fig = plt.figure()
plt.plot(x, y)
fig.savefig('1.png')
```

图片就保存在了当前目录下。

### 曲线的样式和风格

#### 曲线颜色

通过 c 参数可以指定曲线的颜色：

```python
plt.plot(x, y, c='red')
```

这样曲线就是红色的：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20005417.png)

我们还可以指定任意 rgb 的颜色：

```python
plt.plot(x, y, c='#a6c0fe')
```

图像就是我们指定的颜色：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20005459.png)

#### 曲线透明度

通过 alpha 参数指定曲线的透明度：

```python
plt.plot(x, y, alpha=0.3, c='red')
```

可以看到，颜色明显淡很多：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20005628.png)

#### 曲线风格

通过 ls 参数指定曲线的风格：

```python
plt.plot(x, y, ls='dotted')
```

ls 的取值：['solid' | 'dashed', 'dashdot', 'dotted' | (offset, on-off-dash-seq) | `'-'` | `'--'` | `'-.'` | `':'` | `'None'` | `' '` | `''`]

就拿到了点状的曲线：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20005915.png)

#### 点风格

通过 marker 参数可以设置点风格：

```python
plt.plot(x, y, marker='8')
```

marker 的值还可以是 `*`、`^` 等符号。

这样就会把点强调出来：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20010107.png)

## plt.bar 柱状图

`plt.bar()` 用来绘制柱状图，参数主要有三个：第一个参数是索引，第二个参数是数据值，第三个参数是条形的宽度。

示例：

```python
x = [1, 2, 3, 4, 5]
y = [2, 4, 6, 8, 10]
plt.bar(x, y)
```

生成的柱状图为：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20010454.png)

我们可以指定条形的宽度：

```python
plt.bar(x, y, 0.5)
```

条形就变细了：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20010530.png)

还可以使用 barh 生成横向的条形图：

```python
plt.barh(x, y)
```

图形就是这样的：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20010625.png)

## plt.hist 直方图

直方图是一个特殊的柱状图，又叫做密度图，是一定范围内的数字出现的频次。

`plt.hist()` 的参数：

- bins
  可以是一个 bin 数量的整数值，也可以是表示 bin 的一个序列。默认值为10
- normed
  如果值为 True，直方图的值将进行归一化处理，形成概率密度，默认值为 False
- color
  指定直方图的颜色。可以是单一颜色值或颜色的序列。如果指定了多个数据集合,例如DataFrame对象，颜色序列将会设置为相同的顺序。如果未指定，将会使用一个默认的线条颜色
- orientation
  通过设置 orientation 为 horizontal 创建水平直方图。默认值为 vertical

用代码表示就是：

```python
x = [1, 1, 2, 2, 2, 3, 3, 4, 5, 5, 6, 7, 7, 7, 7, 8, 9, 9, 10]
plt.hist(x, bins=20)
```

生成的直方图为：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20011125.png)

## plt.pie 饼图

饼图适合展示各部分占总体的比例，条形图适合比较各部分的大小。

`pie()`，饼图也只有一个参数 x。

当数据中的数字之和大于 1 时，将绘制出完整的饼图：

```python
arr = [11, 22, 31, 15]
plt.pie(arr)
```

绘制出的饼图为：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20011441.png)

如果数据中数字的和小于 1，则绘制出来的饼图将会是不完整的：

```python
arr = [0.2, 0.3, 0.1, 0.2]
plt.pie(arr)
```

饼图是有缺口的：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20011610.png)

我们可以给每个区块指定标签：

```python
arr = [11, 22, 31, 15]
plt.pie(arr, labels=['a', 'b', 'c', 'd'])
```

饼图就有标签了：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20011757.png)

这些标签太靠外了，我们想要把它们放到图片里面，可以指定 labeldistance 参数，设置标签距离圆心的距离：

```python
arr = [11, 22, 31, 15]
plt.pie(arr, labels=['a', 'b', 'c', 'd'], labeldistance=0.3)
```

标签就在图片里面了：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20011949.png)

我们还可以通过设置 autopct 参数，让饼图中显示每一份占的百分比：

```python
arr = [11, 22, 31, 15]
plt.pie(arr, labels=['a', 'b', 'c', 'd'], labeldistance=0.3, autopct='%.6f%%')
```

饼图中就出现了百分比：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20012125.png)

还可以使用 shadow 参数给饼图加阴影，使用 explode 参数把饼图拆分开：

```python
arr = [11, 22, 31, 15]
plt.pie(arr, labels=['a', 'b', 'c', 'd'], labeldistance=0.3, shadow=True, explode=[0.2, 0.3, 0.2, 0.4])
```

饼图就有了阴影，也拆开了：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20012329.png)

## plt.scatter 散点图

- 自变量的变化会导致因变量的变化
- 因变量随自变量而变化的大致趋势

先来创建两个随机数组：

```python
# array([5, 3, 2, 2, 2, 9, 8, 7, 2, 4, 0, 7, 2, 0, 6, 4, 1, 4, 4, 1])
x = np.random.randint(0, 10, size=(20,))
# array([5, 2, 6, 5, 6, 4, 9, 5, 7, 0, 9, 1, 9, 9, 8, 4, 2, 0, 7, 0])
y = np.random.randint(0, 10, size=(20,))
```

然后，绘制散点图：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20012719.png)

随机数的散点图是无规律的。

我们还可以画一下二次函数的散点图：

```python
x = x
y = x ** 2
plt.scatter(x, y, c='rby')
```

这个图形就很有规律了：

![img](matplotlib-basic.assets/Tue,%2007%20Apr%202020%20012840.png)