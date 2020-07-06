## 匿名函数

匿名函数的关键字是 lambda。匿名函数在高阶函数中应用十分广泛，它能极大地简化代码。

比如这个经典的函数定义和调用的代码：

```python
def func(a, b):
    c = a + b
    return c
print(func(1, 2))
```

如果使用匿名函数，只需要两行代码即可：

```python
f = lambda a, b: a + b
print(f(1, 2))
```

甚至一行代码就能实现：

```python
print((lambda a, b: a + b)(1, 2))
```

在上面的代码中：

- lambda 和 def 的作用类似，用来声明要定义一个函数
- `a, b` 和 `(a, b)` 的表达含义类似，用来声明形参
- `: a + b` 和 `return a + b` 的含义类似，用来声明返回值

在匿名函数中的形参可以接受位置参数，动态位置参数，默认参数，动态关键字参数，也可以什么都不写。

匿名函数的冒号 `:` 后面接的是函数的返回值，只能返回一个数据，而且是必须要写的。

当 for 循环和匿名函数一起使用时，会有一个坑：如果是先循环后调用，for 循环的变量会使用最后一个，而不是期间每个元素的值：

```python
lst = []    # [func,func,func]
for i in range(3):
    lst.append(lambda :i)
    
for j in lst:
    print(j())
```

输出的结果为：

```python
2
2
2
```

当第一个循环结束时，列表中存储的值为三个匿名函数的内存地址。每个匿名函数实际上只有一个内容：返回 i。但是在调用之前，这一行代码并不会执行，函数中记录的还是一个 i。当循环结束，我们调用这些函数时，i 已经变成了 2。虽然前两个函数被加入到列表中时 i 的值还是 1 和 2，但它们没有被记录在函数中。

我们可以把上面的代码拆分开，这样看起来会直观些：

```python
lst = []
for i in range(3):
    def func():
        return i
    lst.append(func)
for j in lst:
    print(j())
```

如果将匿名函数和 for 循环封装在列表推导式中，会更有迷惑性：

```python
g = [lambda :i+1 for i in range(3)]
print([em() for em in g])

输出的结果为：
[3, 3, 3]
```

对于生成器表达式，情况又发生了变化——因为当函数被拿出来之后，循环才会向下走：

```python
g = (lambda :i+1 for i in range(3))
print([em() for em in g])

输出的结果为：
[1, 2, 3]
```