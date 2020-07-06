## `f-strings` 详解

`f-strings` 在 [字符串格式化](format.md) 的那一部分已经有所讨论。其实当时已经讨论得差不多了，今天只是稍微地有一点点补充。主要还是复习。

`f-strings` 的基本结构是这样的：

```python
name = '宝元'
age = 18
sex = '男'
msg1 = F'姓名：{name}，性别：{age}，年龄：{sex}'  # 大写字母也可以
msg = f'姓名：{name}，性别：{age}，年龄：{sex}'   # 建议小写
print(msg)
```

输出的结果为：

```python
姓名：宝元，性别：18，年龄：男
```

`f-strings` 就是在字符串的引号前面加上一个字母 `f`。字母大小写都可以，但是推荐使用小写。`{}` 中除了可以使用变量外，还可以放入函数：

```python
def func(a,b):
    return a + b
msg = f"运行结果：{func(1,2)}"
print(msg)
```

输出的结果为：

```python
运行结果：3
```

甚至可以在 `{}` 中放入 `input` 函数，让用户输入：

```python
print(f"姓名：{input('请输入姓名：')} 年龄：{input('请输入年龄：')} 性别：{input('请输入性别：')}")
```

输出的结果是：

```python
请输入姓名：alex
请输入年龄：18
请输入性别：男
姓名：alex 年龄：18 性别：男
```

除了字符串，{} 中也可以放入列表和字典：

```python
lst = [1, 2, 32, 34, 45, 5]
msg = f"运行结果：{lst[0:3]}"
print(msg)

dic = {"key": 1, "key1": 22}
msg = f"运行结果：{dic['key1']}"
print(msg)
```

输出的结果为：

```python
运行结果：[1, 2, 32]
运行结果：22
```

`f-string` 可以写成多行的形式，但依然打印成一行：

```python
msg = f"窗前明月{'光'}，" \
      f"玻璃好上{'霜'}。" \
      f"要不及时{'擦'}，" \
      f"一会就得{'脏'}。"
print(msg)
```

输出的结果为：

```python
窗前明月光，玻璃好上霜。要不及时擦，一会就得脏。
```

要想打印多行字符串，还是要使用三对引号：

```python
msg = f"""
窗前明月{'光'},
玻璃好上{'霜'}.
要不及时{'擦'},
一会就得{'脏'}.
"""
print(msg)
```

通过使用三元运算，配合 f-strings，我们可以进一步节省代码：

```python
a = 10
b = 20
msg = f"{a if a < b else b}"
print(msg)
```

同时使用两个括号表示一个可以打印的大括号：

```python
msg = f"{{'alex': 'wusir'}}"
print(msg)
```

输出的结果为：

```python
{'alex': 'wusir'}
```