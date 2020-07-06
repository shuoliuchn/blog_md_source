## 用户交互语句

[TOC]

### `print` 语句

我们在讨论 Python 中基本数据类型时，已经讨论过 print 语句的基本用法，这里只是简单提一下：

```python
a = 123
b = 'abc'
c = b + 'd'
print(a)      # 打印一个变量
print(b, c)   # 打印多个变量
```

### `input` 语句

在 Python 中，用 `input()` 函数实现用户和程序间的交互。`input` 是输入的意思。使用示例如下：

```python
qq_user = input("QQ 账号：")    # 坑 --> 阻塞
qq_pwd = input("QQ 密码：")
print(qq_user, qq_pwd)
```

当程序运行到 `input` 语句时，会发生阻塞，等待用户进行输入。程序会一直保持阻塞状态，除非用户输入内容或终止程序。

需要注意的是，在 Python 3 中 `input` 获取的内容全都是字符串。因为这样的原因，下面的程序会报错：

```python 
num = input("请输入数字：")
print(num + 5)
```

这两行代码的本意是，当用户输入一个数字之后，程序自动输出一个比输入数字大 5 的数字。但是因为 `input` 获取的内容是字符串，字符串是不能和整型数字进行加和操作的，故而程序报错。我们可以使用 `tpye()` 函数查看变量的数据类型。

```python
num = input("请输入数字：")
print(type(num))    # 查看数据类型
```

输出的结果为：

```python
请输入数字：12
<class 'str'>
```

### `int` 和 `str` 语句

通过使用 `int()` 函数可以将字符串转化为整型数据。同样地，也可以使用 `str()` 函数，把整型数据转换成字符串。

```python
a = int('12')	# 字符串转成整型
b = str(23)		# 整型转成字符串
```

需要注意的是，使用 `int()` 函数将字符串转换为整型时，字符串中的内容必须全部都是数字，否则会报错：

```python
>>> int('abc123')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: invalid literal for int() with base 10: 'abc123'
```

那么我们上面的例子就可以改成这个样子：

```python
num = input("请输入数字：")
a = int(num)
print(a + 5)
```

这样的话，当我们输入一个数字之后，通过 `int()` 函数，输入内容转换成为整型，然后就可以进行加和操作了：

```python
请输入数字：3
8
```

也可以把 `inpu()` 操作直接放到 `int()` 函数中：

```python
num = int(input("请输入数字："))
print(num + 5)
```

### 用户交互语句总结

- `input()` 是输入，获取到的内容都是字符串
- `type()` 函数用来查看数据类型
- `int('字符串')` 可以将字符串转换成整型，字符串中的内容必须全部都是数字
- `str(整型)` 函数可以将整型转换成字符串