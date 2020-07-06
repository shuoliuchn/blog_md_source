## 推导式

[TOC]

### 列表推导式

推导式用来创建一些有规律的可变数据结构，能够让代码变得更加简洁。

比如，我们想要通过循环的方法创建一个数字从 1 到 50 的列表，可以这样做：

```python
lst = []
for i in range(1, 51):
    lst.append(i)
print(lst)
```

如果使用列表推导式，我们只需要一行代码：

```python
print([i for i in range(1, 51)])
```

我们就成功创建了一个 `列表推导式`。我们还可以运用字符串的格式化实现更多样化的输出：

```python
print([f"Python{i} 期" for i in range(1, 51)])
```

前面两种是列表推导式的普通循环模式，它的基本结构为：

```
[加工后的变量 for循环]
```

除了普通循环模式，我们还可以通过给循环增加筛选条件，实现筛选模式的列表推导式：

```python
print([i for i in range(1, 51) if i > 25])
```

筛选模式的列表推导式的基本结构为：

```
[加工后的变量 for循环 加工条件]
```

列表推导式还支持嵌套。

对于这样一个嵌套的循环：

```python
lst = []
for i in range(2):
    for j in range(2):
        lst.append(i + j)
print(lst)
```

改成列表推导式结构就成了这样：

```python
print([i + j for i in range(2) for j in range(2)])
```

嵌套的列表推导式同样可以使用条件进行筛选：

```python
print([(i, j) for i in range(5) if i > 3 for j in range(6) if j < 3])
```

虽然列表推导式能节省很多代码，但是最多建议嵌套三层。

生成器可以让代码更加简化。比如，我们想求字符串 `"alex,meet"` 中每个字母 `e` 的索引。如果用 for 循环来写是可以实现的，但是需要多行代码：

```python
s = 'alex,meet'
count = 0
lst = []
for i in s:
    if i == 'e':
        lst.append(count)
    count += 1
print(lst)

输出结果为：[2, 6, 7]
```

而如果使用列表生成器的话，只需要一行代码即可实现：

```python
print([i for i in range(len(s)) if s[i] == 'e'])
```

### 字典推导式和集合推导式

除了列表之外，其他可变数据类型，比如字典字典和集合也可以通过推导式的方法来创建：

```python
# 字典推导式
print({i:i+1 for i in range(3)})    # 字典推导式普通循环模式
print({f"Python{i}":i+1 for i in range(3)})    # {变量:变量  for循环}
print({i:i+1 for i in range(3) if i > 1})    # 字典推导式筛选模式
# {加工后的变量:加工的后的变量 for循环 加工条件}

# 集合推导式
print({i for i in range(3)})    # 集合推导式普通循环模式 {变量 for循环}
print({i for i in range(3) if i >2})    # 集合推导式筛选模式
# {加工后的变量 for循环 加工条件}
```

### 生成器表达式（推导式）

我们前面学到的 [生成器](iterator-generator.md)，除了可以通过函数的方式实现，还可以使用表达式来实现。与列表表达式相似，生成器表达式也有普通模式和筛选模式：

```python
# 普通模式：
g = (i for i in range(3))
# 筛选模式：
g = (i for i in range(3) if i + 1 == 2)
```

生成器推导式的好处有三个：简化代码，提高逼格和提高可读性。

同列表表达式一样，生成器推导式用于生成一些有规律的数据。当我们需要生成的数据较大时，建议使用生成器推导式。