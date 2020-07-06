## 集合

[TOC]

### 集合概览

集合也是 Python 中的基本数据类型之一。

集合最重要的一个特点是天然去重。

集合的关键字是 set。

空集合的表示方法只有一种：`set()`。

集合是一种无序的，可变的，可迭代的，元素唯一且不可哈希的数据类型。

集合可以被看作是一种没有值的字典：

- 都用 `{}` 标识
- 集合的元素是不可变的（可哈希）
- 具有唯一性才做到去重
- 无序，可变，可迭代

我们可以这样定义一个集合：

```python 
s = {1, 2, 3, 4}
print(type(s))
```

输出的结果为：

```python
<class 'set'>
```

如果在定义集合时出现了重复元素，集合会自动去重复：

```python
s = {1, 2, 3, 4, 1, 1, 12, 33, 3, '421', 21, 12, 3}
print(s)
```

输出的结果为：

```python
{1, 2, 3, 4, 33, 12, '421', 21}
```

如果多次打印上面的内容，会发现集合是无序的（对于纯数字集合这种效果不是很明显。如果里面又多种数据类型，无序性会很明显）。

在 Python 中，能够存储数据的结构被称作为容器。我们学过的能作为容器的数据类型有：列表、元素和字典。很显然，集合也是一种容器。

### 集合的增加

`.update()` 方法可以将输入的参数迭代添加到集合中，参数必须是可迭代数据类型：

```python
s = set()
s.update('alex')
print(s)
```

输出的结果为：

```python
{'e', 'l', 'a', 'x'}
```

`.add()` 方法可以将单独的元素直接添加到集合中：

```python
s = set()
s.add('alex')
print(s)
```

输出的结果为：

```python
{'alex'}
```

### 集合的删除

`.pop()` 方法会随机删除集合中的元素，并将删除的元素返回：

```python
s = {"a", "b", 3, "c"}
print(s.pop(), s)
```

这个式子多次运行后会发现，每次删除掉的数据都是不同的。正因为这种数据删除方法的随机性和不确定性，十分不建议在编程时使用。

`.remove()` 方法可以指定元素删除集合中的内容：

```python 
s = {"a", "b", 3, "c"}
s.remove('b')
print(s)
```

输出的结果为：

```python
{'c', 3, 'a'}
```

`.clear()` 方法用来将集合清空：

```python
s = {"a", "b", 3, "c"}
s.clear()
print(s)
```

输出的结果为：

```python
set()
```

这里需要注意的是，为了防止和空字典的表示方法 `{}` 冲突，空集合只有 `set()` 一种表示方法。

### 集合的修改

集合没有直接进行修改的方法，不过可以通过下面两种方式间接改变集合的内容：

1. 先删后加（或者先加后删）
2. 转换为列表数据类型后进行修改

### 集合的查找

因为集合是无序的，所以不能通过索引查找。

好在集合可以迭代，可以通过 for 循环进行查看：

```python
s = {"a", "b", 3, "c"}
for i in s:
    print(i)
```

### 集合关系

同数学中集合的概念很相似，Python 中的集合也可以进行交集、并集、差集、补集、超级和子集等操作：

#### 交集

交集用来找出两个集合中相同的元素，用 `&` 表示：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
print(python & linux)
```

输出的结果为：

```python
{'海绵', '大圣'}
```

交集也可以使用集合的 intersection 方法来计算：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
print(python.intersection(linux))
```

输出的结果同样为：

```python
{'大圣', '海绵'}
```

#### 并集

并集是将两个集合合并，并将重复元素去除掉，用 `|` 表示：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
print(python | linux)
```

输出的结果为：

```python
{'岳新力', 'meet', '海绵', '孙一帆', '大圣', 'alex'}
```

并集也可以使用集合的 union 方法来实现：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
print(python.union(linux))
```

输出的结果同样为：

```python
{'大圣', 'alex', 'meet', '孙一帆', '海绵', '岳新力'}
```

#### 差集

差集用来消除前面集合中与后面集合重复的元素，用 `-` 表示：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
print(python - linux)
print(linux - python)
```

输出的结果为：

```python
{'岳新力', '孙一帆'}
{'alex', 'meet'}
```

差集可以使用集合的 difference 方法实现：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
print(python.difference(linux))
print(linux.difference(python))
```

结果同样为：

```python
{'海绵', '大圣'}
{'孙一帆', 'meet', '海绵', 'alex', '岳新力', '大圣'}
{'孙一帆', '岳新力'}
{'meet', 'alex'}
```

#### 补集

补集也称作反差集，对称差集，是两个集合中不相同元素组成的集合，用 `^` 表示：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
print(python ^ linux)
```

输出的结果为：

```python
{'岳新力', 'alex', '孙一帆', 'meet'}
```

对称差集可以使用 symmetric_difference 方法获得：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
print(python.symmetric_difference(linux))
```

输出的结果同样为：

```python
{'岳新力', '孙一帆', 'alex', 'meet'}
```

symmetric_difference 方法会生成一个新的集合。如果想要在原集合上面直接修改，可以使用 symmetric_difference_update 方法：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
print(python.symmetric_difference_update(linux))
print(python)
```

输出的结果为：

```python
None
{'孙一帆', 'alex', '岳新力', 'meet'}
```

#### 超集

超集也称作父集，用来判断后面集合中是否每一个元素都在前一个集合中且两个集合不相同，若都在且两个集合不完全一样，返回 True，若不都在或两个集合完全一样，返回 False。超集用 `>` 表示：

```python
python = {"海绵", "孙一帆", "岳新力", "大圣"}
linux = {"海绵", "大圣", "meet", "alex"}
l_son = {'meet'}
print(python > linux)
print(python > python)
print(linux > l_son)
```

输出的结果为：

```python
False
False
True
```

#### 子集

子集用来判断前面集合中的每一个元素是否都在后一个集合中且两个元素都不相同，若都在且两个集合不完全一样，返回 True，若不都在或两个集合完全一样，返回 False。超集用 `<` 表示：



在最后，补充一点感想。听到一句话对我很有感触。这句话是，计算机不会出错，错的都是人。

我觉得计算机不会出错这件事反映出来的不是人笨，而是计算机还不够智能。会随机地出错，是计算机走向智能的一大步。

集合的 `.pop()` 方法，可以随机地删除数据，也就是制造错误，或许在人工智能的研究中很有用。