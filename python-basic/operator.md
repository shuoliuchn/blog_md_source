## 运算符

[TOC]

### 比较运算符

比较运算符主要有六个：

1. `>`：大于
2. `<`：小于
3. `>=`：大于等于
4. `<=`：小于等于
5. `==`：等于
6. `!=`：不等于

比较运算返回的值为 `True` 或 `False`。

### 算术运算符

Python中的算术运算符有七个：

1. `+`：加和
2. `-`：相减
3. `*`：相乘
4. `/`：相除
5. `//`：整除 | 地板除（向下取整）
6. `**`：幂运算
7. `%`：取余（模）

算术运算主要用于数字的计算。字符串也可以用 `+` 和 `*` 进行拼接。

### 赋值运算符

赋值运算符为 `=`。在 Python 中，为了输入简便，还从其中算术运算符中衍生出了七种赋值运算符：`+=`，`-=`， `*=`， `/=`， `//=`， `**=`， `%=`。它们的用法和含义如下：

```python
a = 10
b = 2
b += 1	# b = b + 1
a -= 1  # a = a - 1
a *= 2  # a = a * 2
a /= 2  # a = a / 2
a //= 2 # a = a // 2
a **= 2 # a = a ** 2
a %= 2  # a = a % 2
```

### 逻辑运算符

逻辑运算符有三个：与（`and`，并且）、或（`or`）、非（`not`，不是）。

逻辑运算的优先级是 `() > not > and > or`，查找顺序为从左向右。例如：

```python
3>2 and 4>2 or True and not False and True
# 先运算比较
True and True or True and not False and True
# 再运算not
True and True or True and True and True
# 运算and
True or True
# 最后运算or
True
```

当数字之间进行逻辑运算时，有这样一套规则：

```python
and 数字进行逻辑运算时：
    两边都不为 0 和 False 时，选择 and 后边的内容
    两边都为假时，选择 and 前的内容
    一真一假选择假

or 数字进行逻辑运算时：
    两边都不为 0 和 False 时，选择 or 前边的内容
    两边都为假时，选择 or 后边的内容
    一真一假选择真
```

官方给出的运算规则是这个样子的：

|    操作 | 结果                                   |
| ------: | -------------------------------------- |
|  x or y | 如果 x 为假，选择 y，否则选择 x        |
| x and y | 如果 x 为假，选择 x，否则选择 y        |
|   not x | 如果 x 为假，返回 True，否则返回 False |

可以通过下面的示例来找到这些规律：

```python
print(1 and 3)
print(0 and 8)
print(9 and 4)
print(False and 5)
```

### 成员运算符

在 Python 中，成员运算符有两个：

- a in b：用于判断 a 是否在 b 中
- a not in b：用于判断 a 是否不在 b 中

具体使用示例：

```python
name = "alex"
msg = input(">>>")
if name in msg:
    print(111)
else:
    print(222)
```

输出结果为：

```python
>>>alexad
111
>>>alecxaa
222
```

