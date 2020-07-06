## 迭代器和生成器

[TOC]

### 迭代器

迭代器就是用来将可迭代对象的值一个一个取出来的工具。

我们学过的可迭代的数据类型有：字符串、列表、字典、元组、集合

不可迭代的数据类型有：整型、布尔值

Python 中规定，只要是具有 `__iter__()` 方法就是**可迭代对象**：

```python
str.__iter__()
list.__iter__()
tuple.__iter__()
dict.__iter__()
set.__iter__()
```

可迭代对象可以通过 for 循环获取每一个元素，且可以重复取值：

```python
s = 'alex'
for i in s:
    print(i)
for j in s:
    print(j)
```

输出的结果为：

```python
a
l
e
x
a
l
e
x
```

我们可以使用可迭代对象的 `.__iter__()` 方法，将其转化为迭代器。可以通过 `.__next__()` 方法获取下一位的值：

```python
lst = [1, 2, 3, 4, 5]
l = lst.__iter__()    # 生成一个迭代器对象
print(l)
print(l.__next__())    # 获取下一位元素
print(l.__next__())
print(l.__next__())
print(l.__next__())
print(l.__next__())
```

输出的结果为：

```python
<list_iterator object at 0x0000026525B9B588>
1
2
3
4
5
```

需要注意的是，迭代器中有多少个元素，就只能使用多少次 `.__next__()` 方法，如果使用次数超过元素个数，就会报错：

```python
File "C:/Users/Sure/PyProject/week03/day11/exercise.py", line 59, in <module>
    print(l.__next__())
StopIteration
```

在 Python 中，有 `.__iter__()` 和 `.__next__()` 方法的就是一个迭代器。

不难看出，文件句柄也是一个迭代器：

```python
f = open('user_info', 'a', encoding='utf-8')
f.__iter__()
f.__next__()
```

这里说一句，当我们使用 id 函数获取到的内存地址，并不是数据真正存放的位置，只是一串供我们参考的数字而已，不可当真。

需要注意的是，当对同一个可迭代对象多次使用 `.__iter__()` 方法创建迭代器的时候，我们是创建了多个生成器，这些生成器之间不会相互影响：

```python
lst = [1, 2, 3, 4, 5]
l1 = lst.__iter__()    # 这是一个迭代器1
l2 = lst.__iter__()    # 这是一个迭代器2
l3 = lst.__iter__()    # 这是一个迭代器3
print(l1.__next__())
print(l2.__next__())
print(l3.__next__())
print(l2.__next__())
print(l3.__next__())
print(l3.__next__())
```

输出的结果为：

```python
1
1
1
2
2
3
```

本质上来讲，for 循环的底层，就是将可迭代对象转换为生成器，通过循环迭代，获取每一个元素的：

```python
s = 'alex'
s1 = s.__iter__()
while True:
    try:    # 尝试运行一下缩进体中的代码
        print(s1.__next__())
    except StopIteration:
        break
```

输出的结果为：

```python
a
l
e
x
```

除了使用可迭代对象的 `.__iter__()` 和 `.__next__()` 方法创建和操作迭代器，我们还可以使用 Python 的内置函数，`iter()` 和 `next()` 来实现同样地功能：

```python
lst = [1, 2, 3, 4, 5]
l = iter(lst)
print(next(l))
print(next(l))
print(next(l))
```

输出结果为：

```python
1
2
3
```

需要注意的是，在 Python 2 中，创建和使用迭代器时只能使用内置函数 `iter()` 和 `next()`，迭代器的 `.__iter__()` 和 `.__next__()` 方法不可用；在 Python 3 中，既可以使用内置函数 `iter()` 和 `next()`，也可以使用 `.__iter__()` 和 `.__next__()` 方法创建和使用迭代器。但是推荐使用兼容性更高的内置函数 `iter()` 和 `next()`。

可迭代对象与迭代器的比较：


- 可迭代对象：str list tuple ...
	- 具有 `.__iter__()` 方法的就是一个可迭代对象
	- 优点：
		1. 使用灵活（每个可迭代对象都有自己的方法）
		2. 能够直接且直观地查看元素个数
	- 缺点：
		- 占内存
	- 应用：当内存空间大，数据量比较少的情况，建议使用可迭代对象

- 迭代器：文件句柄就是一个迭代器
	- 具有 `.__iter__()` 和 `.__next__()` 方法的，就是一个迭代器
	
	- 优点：
		
		- 节省内存
	- 缺点：
		
		1. 只能一个方向执行
		2. 一次性的
		3. 不能灵活操作，不能直接且直观查看元素个数
	- 应用：当内存小，数据量大的情况，建议使用迭代器


迭代器除了节省内存之外，似乎没有什么好处，但是广泛应用于 Python 编程过程中，就是因为它能大量节省内存空间。在编程过程中，我们常常会进行空间和时间的抉择：

- 时间换空间：迭代器，生成器，用大量的时间来节省空间的使用
- 空间换时间：可迭代对象，使用大量的空间来节省时间

迭代器同样具有 `.__iter__()` 方法，因此也是一个可迭代对象，可以直接被for循环：

```python
lst = [1, 2, 3, 4]
l = iter(lst)
for i in l:    # for循环可以直接循环迭代器
    print(i)
```

### 生成器

生成器的本质就是一个迭代器。

生成器和迭代器的最大区别为：

- 迭代器：比如文件句柄，是通过数据转换，由 Python 自带提供的
- 生成器：程序员自己编写实现

生成器的目的为：不再通过数据转换实现迭代器，而是通过代码编写实现。

生成器的定义方式有两种：

1. 基于函数实现生成器
2. 使用表达式实现生成器

我们可以通过这个方式来定义和使用一个函数：

```python
def func():
    print(1)
    return 5
print(func())
```

输出的结果为：

```python
1
5
```

如果我们把函数中的return替换成yield，就创建了一个生成器：

```python
def func():
    print(1)
    yield 5    # 函数体中存在yield就是定义了一个生成器
print(func())    # 创建一个生成器对象
```

输出的结果为：

```python
<generator object func at 0x000001D59DAA2EB8>
```

`func` 函数调用时，会生成一个生成器对象，`print` 打印出来的就是这个生成器对象的内存地址。

生成器的特点是：惰性机制，也就是只有使用 next 方法调用生成器时，才会开始执行生成器的代码。即便是在创建生成器对象时，也不会运行生成器中的内容。

yield 和 return 的部分功能很像

我们可以设定多个 yield，每次使用 next 函数，就会运行到下一个 yield，直至最后：

```python
def func():
    yield 1
    yield 2
    yield 3
g = func()
print(next(g))
print(next(g))
print(next(g))
```

输出的结果为：

```python
1
2
3
```

同迭代器类似，也可以创建多个生成器对象，这些生成器对象彼此独立，互不干扰：

```python
def func():
    yield 1
    yield 2
    yield 3
g1 = func()
g2 = func()
g3 = func()
print(g1, g2, g3)
print(next(g1))
print(next(g2))
print(next(g3))
print(next(g2))
print(next(g3))
print(next(g3))
```

输出的结果为：

```python
<generator object func at 0x0000026AC9932EB8> <generator object func at 0x0000026AC9932F10> <generator object func at 0x0000026AC9932F68>
1
1
1
2
2
3
```

三个生成器的内存地址各不相同，使用 next 方法也是互不影响。

如果 yield 后面什么也不写，默认返回的值为 None：

```python
def func():
    yield
print(func().__next__())

输出的结果为： None
```

同 return 类似，yield 后面也可以接任意数据类型：

```python
def func():
    yield [1, 2, 3, 4, 5]
print(func().__next__(), type(next(func())))

def foo():
    yield {'key': 1}
print(next(foo()), type(next(foo())))

def f():
    def f1():
        print(123)
    yield f1
print(next(f()), type(next(f())))

def f2():
    yield 1, 2, 3, 4, 5
print(next(f2()), type(next(f2())))
```

输出的结果为：

```python
[1, 2, 3, 4, 5] <class 'list'>
{'key': 1} <class 'dict'>
<function f.<locals>.f1 at 0x0000022675D8DBF8> <class 'function'>
(1, 2, 3, 4, 5) <class 'tuple'>
```

生成器的好处同迭代器一样，也是可以节省大量的内存空间。

除了 yield 方法，我们还可以使用 yield from 方法来逐个返回可迭代对象中的元素：

```python
def func():
    yield from [1, 2, 3, 4, 5]    # 将列表中的元素逐个返回
g = func()
print(next(g))
print(next(g))
print(next(g))
print(next(g))
print(next(g))
```

输出的结果为：

```python
1
2
3
4
5
```

如果两个yield from同时存在，会先将前面的可迭代对象逐个返回之后，再返回后面的可迭代对象：

```python
def func():
    yield from [55, 44, 66, 77, 88]
    yield from [1, 2, 3, 4, 5]
g = func()
print(next(g))
print(next(g))
print(next(g))
print(next(g))
print(next(g))
print(next(g))
print(next(g))
print(next(g))
print(next(g))
print(next(g))
```

输出结果为：

```python
55
44
66
77
88
1
2
3
4
5
```

### 迭代器和生成器总结

生成器一定是一个迭代器，但是迭代器不一定是一个生成器；

迭代器一定是一个可迭代对象，但是可迭代对象不一定是一个迭代器。

生成器的本质是一个迭代器，迭代器的本质是一个可迭代对象。

迭代器和生成器的优点：

- 节省空间

迭代器和生成器的缺点：

1. 不能直接使用元素
2. 不能直观查看元素个数
3. 使用不灵活
4. 稍微消耗时间
5. 操作是一次性的，不可逆的

当数据量特别巨大时，要记得使用生成器

区分迭代器和生成器：

```python
lst = [1, 2, 3]
l = lst.__iter__()

def func():
    yield 1
g = func()

print(l, g)
```

输出的结果为：

```python
<list_iterator object at 0x0000021C1E7BB5C0> <generator object func at 0x0000021C1E722EB8>
```

1. 将对象直接用 `print` 函数打印出来，查看内存地址。如果显示的是 `iterator`，就是迭代器；如果是 `generator`，就是生成器（主推荐的）；
2. 查看是否可用 `.send()` 方法，如果可用，则是生成器，不可用则是迭代器。

yield 的特点：

1. yield 能返回多个数据，以元组的形式存储
2. yield 能返回各种数据类型（Python 中的任意对象）
3. yield 能够写入多个并且都可以执行
4. yield 能够记录执行的位置
5. yield 后面不写内容，默认返回 None
6. yield 都是将数据一次性返回

yield from 的特点：

- 将可迭代对象逐个返回

可迭代对象、迭代器和生成器的比较：

- 可迭代对象：具有 `.__iter__()` 方法的就是可迭代对象
- 迭代器：具有 `.__iter__()` 和 `.__next__()` 方法的就是一个迭代器
- 生成器：基于函数创建的生成器，函数体中必须存在 yield