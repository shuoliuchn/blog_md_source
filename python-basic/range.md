## range

在 Python 中，通过 range 可以达到循环数字的效果：

```python
for i in range(1, 5):
    print(i)
```

输出的结果为：

```python
1
2
3
4
```

对于下面一行代码：

```python
a = range(0, 5)
print(a)
```

在 Python 2 中，会打印出列表 `[0, 1, 2, 3, 4]`；在 Python 3 中则会被打印为 `range(0, 5)`。可以通过 `list()` 函数将 `range` 对象转化为列表：

```python
a = range(0, 5)
print(list(a))

```

输出的结果为：

```python
[0, 1, 2, 3, 4]
```

`range` 中的参数和 `切片` 极其相似，range 的索引是几，其对应的值就是几：

```python
for i in range(0, 51, 2):
    print(i)	# 打印0-50间的所有偶数
for i in range(1, 51, 2):
    print(i)	# 打印0-50间的所有奇数
```

`range` 与 `切片` 所不同的是，`range` 的终止位置为负数时，表示的是负数位置，而不是从右向左数的位置：

```python
for i in range(10, -11, -1):
    print(i)	# 打印10到-10的数字
```

一般情况下，我们可以直接写入一个终止位置。这时，起始位置默认为 `0`，步长默认为 `1`：

```python
print(list(range(5)))
```

输出结果为：

```python
[0, 1, 2, 3, 4]
```

需要注意，如果要设置步长，一定要把变量写全，要包含起始位置。

很显然，`range`是一个可迭代对象。