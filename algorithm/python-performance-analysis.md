## Python 数据结构性能分析

除了使用大 O 表示法，我们也可以通过 timit 模块来对一段代码的运行时间进行测试。根据时间的开销，我们可以评定一段代码的优劣。

在列表的操作有一个非常常见的编程任务就是是增加一个列表。我们马上想到的有两种方法可以创建更长的列表，可以使用 append 方法或拼接运算符。但是这两种方法那种效率更高呢。这对我们来说很重要，因为它可以帮助我们通过选择合适的工具来提高你自己的程序的效率。

需求：实例化一个空列表，然后将 0-n 范围的数据添加到列表中。（四种方式）

测试代码运行时间我们需要用到 timeit 模块：

- timeit 模块：该模块可以用来测试一段 Python 代码的执行速度/时长。
- Timer 类：该类是 timeit 模块中专门用于测量 Python 代码的执行速度/时长的。原型为：`class timeit.Timer(stmt='pass', setup='pass')`。
  - stmt 参数：表示即将进行测试的代码块语句。
  - setup：运行代码块语句时所需要的设置。
  - timeit 函数：`timeit.Timer.timeit(number=100000)`，该函数返回代码块语句执行 number 次的平均耗时。

```python
from timeit import Timer

def test01():
    alist = []
    for i in range(1000):
        alist.append(i)
    return alist
def test02():
    alist = []
    for i in range(1000):
        alist = alist + [i]
    return alist
def test03():
    alist = [i for i in range(1000)]
    return alist
def test04():
    alist = list(range(1000))
    return alist

if __name__ == '__main__':
    timer1 = Timer(stmt='test01()', setup='from __main__ import test01')
    t1 = timer1.timeit(100)    # 0.029332300000000002
    timer2 = Timer(stmt='test02()', setup='from __main__ import test02')
    t2 = timer2.timeit(100)    # 0.20736
    timer3 = Timer(stmt='test03()', setup='from __main__ import test03')
    t3 = timer3.timeit(100)    # 0.003999099999999978
    timer4 = Timer(stmt='test04()', setup='from __main__ import test04')
    t4 = timer4.timeit(100)    # 0.001777899999999999
    print(t1, t2, t3, t4)
```

我们看到，第四种使用内置函数创建列表的方式，代码执行速度最快。使用列表生成式创建的列表也很迅速。而使用列表加和拼接方式扩展列表显然不是一个明智之举，这是因为每次都要创建新列表，而拼接列表的操作也比较耗时。

如果使用的是 jupyter，可以使用魔法方法来给指定的 cell 测试运行时间：

```python
%%timeit
print('aaa')
```

