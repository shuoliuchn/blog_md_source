## 闭包

在编程时，我们会处理到很多数据。但是对于一些数据，我们只想使用，不想修改。我们可以使用 `闭包` 来防止不经意间的数据修改。

闭包的作用主要有两个：

1. 保护数据安全
2. 保护数据干净性

满足下面两个条件的函数就是一个实用的闭包：

1. 在嵌套函数内，使用非全局变量（且不使用本层变量）
2. 将嵌套函数本身返回

我们可以用`.__closure__`方法来查看一个函数是否是一个闭包。当返回值为None时，说明该函数不是闭包，当返回值中有变量的内存地址时，说明该函数是一个闭包。

例如，下面的函数就是一个闭包：

```python
def func():
    a = 10    # 自由变量
    def foo():
        print(a)
    return foo
f = func()
print(f.__closure__)
```

输出的结果为：

```python
(<cell at 0x000001F7D6277648: int object at 0x00000000669D8190>,)
```

方法中的 `closure` 就是 `闭包` 的意思。返回的 `cell` 是一个 `单元格` 对象，用来储存闭包中需要使用到的变量。

当函数执行完成后（也就是 `fun()` 这个步骤），函数体会被销毁。但是闭包 `foo` 被返回给了 `f`，还会被用到，所以照常存在。而闭包中需要使用的变量 `a`，此时已经不在函数 `func` 中，因为 `func` 本身已经不复存在。变量 `a` 在函数销毁后，升级为 `自由变量`。

闭包是怎么做到保护数据的作用的呢？

比如我们现在有这样一个需求：使用一个函数，每天输入当天营业额，打印出这几天的平均营业额。

如果我们把营业额的数据储存在列表中，放到全局，可以这样实现功能：

```python
lst = []
def ave_turnover(turnover_today):
    lst.append(turnover_today)
    ave = sum(lst) / len(lst)
    print(ave)
ave_turnover(12000)
ave_turnover(15000)
ave_turnover(14000)
ave_turnover(12000)
ave_turnover(25000)
ave_turnover(21000)
ave_turnover(9000)
```

输出的结果为：

```python
12000.0
13500.0
13666.666666666666
13250.0
15600.0
16500.0
15428.57142857143
```

如果有一天，我们不小心在全局对 `lst` 列表做了修改：

```python
lst = []
def ave_turnover(turnover_today):
    lst.append(turnover_today)
    ave = sum(lst) / len(lst)
    print(ave)
ave_turnover(12000)
ave_turnover(15000)
lst[1] = 10
ave_turnover(14000)
ave_turnover(12000)
ave_turnover(25000)
ave_turnover(21000)
ave_turnover(9000)
```

输出的结果为：

```python
12000.0
13500.0
8670.0
9502.5
12602.0
14001.666666666666
13287.142857142857
```

这就导致了数据的改变，使得第三天之后的数据全都不准确。

如果我们把重要的数据 `lst` 封装到闭包中保护起来，就不会有这种烦恼了：

```python
def ave_turnover():
    lst = []
    def inner(turnover_today):
        lst.append(turnover_today)
        ave = sum(lst) / len(lst)
        print(ave)
    return inner
f = ave_turnover()
f(12000)
f(15000)
lst = [10]
f(14000)
f(12000)
f(25000)
f(21000)
f(9000)
```

输出的结果为：

```python
12000.0
13500.0
13666.666666666666
13250.0
15600.0
16500.0
15428.57142857143
```

对于闭包，除了 `.__closure__` 方法之外，还有可以用来查看自由变量的 `.__code__.co_freevars` 方法，和可以用来查看局部变量的 `.__code__.co_varnames` 方法：

```python
def ave_turnover():
    lst = []
    def inner(turnover_today):
        lst.append(turnover_today)
        ave = sum(lst) / len(lst)
        print(ave)
    return inner
f = ave_turnover()
print(f.__closure__)             # 判定是否是一个闭包
print(f.__code__.co_freevars)    # 查看自由变量
print(f.__code__.co_varnames)    # 查看局部变量
```

输出的结果为：

```python
(<cell at 0x0000027A31A27648: list object at 0x0000027A31AD8C08>,)
('lst',)
('turnover_today', 'ave')
```

没有将嵌套的函数返回的函数也可以是一个闭包，但是这个闭包没有办法被使用：

```python
def func():
    a = 10
    def foo():
        print(a)
    print(foo.__closure__)
func()
```

输出的结果为：

```python
(<cell at 0x000001BE85EA7648: int object at 0x00000000669D8190>,)
```

闭包也可以通过外层函数加两个括号的方法来调用：

```python
def func():
    a = 10
    def foo():
        print(a)
    return foo
func()()
```

外层函数的参数也是局部变量，所以内层函数使用外层参数的形式也可以构成闭包：

```python
def wrapper(a, b):
    def inner():
        print(a)
        print(b)
    return inner
a = 11
b = 12
ret = wrapper(a, b)
print(ret.__closure__)
print(ret.__code__.co_freevars)
print(ret.__code__.co_varnames)
ret()
```

返回的结果为：

```python
(<cell at 0x0000021A67AFB648: int object at 0x00000000702D81B0>, <cell at 0x0000021A67AFB9A8: int object at 0x00000000702D81D0>)
('a', 'b')
()
11
12
```

闭包的应用场景主要有两个：

1. 装饰器
2. 防止数据被误改动

