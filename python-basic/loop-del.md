## 循环删除的坑

有这样一个列表：`lst = [11, 22, 33, 44, 55]`，现在我们想要把列表中的每个元素都删除掉，但是不能用 `.clear()` 方法。我们很理所当然地会想到使用 for 循环逐个删除：

```python
lst = [11, 22, 33, 44, 55]
for i in lst:
    lst.remove(i)
print(lst)

输出的结果为：[22, 44]
```

居然没有删除干净。

这是 python 中循环的自主计数和列表的自动补位共同影响下的结果。在循环第一圈时，循环操作从索引值为 0 的位置找元素，返回了 11，被成功删除。当列表删除了 11 之后，后面的元素会自动补位上来，此时，22 的索引变成了 0，33 的索引变成了 1。随后，进入第二圈循环，循环操作会自主把计数加一，开始找索引值为 1 的元素，也就是 33，被成功删除，而 22，被忽略掉了。如此一来，所有偶数索引的元素都被删除，奇数索引的元素被保留下来。

为了应对这种循环删除的坑，我们可以采取两种方式[^1]。

方式一，使用range获取字符串长度，控制循环次数，保证能够完全删除：

```python
lst = [11, 22, 33, 44, 55]
for i in range(len(lst)):
    lst.pop()
print(lst)
```

方式二，通过复制一个列表，通过循环复制过的列表，控制循环次数：

```python
lst = [11, 22, 33, 44, 55]
for i in lst.copy():
    lst.remove(i)
print(lst)
```

除了列表之外，字典和集合也深受循环删除的坑的困扰。与列表不能删除干净不同的是，字典和集合在迭代过程中是不可以修改字典的长度的，一旦有修改，就会报错。也就是说，一旦在迭代过程中删除键值对，程序会直接报错：

```python
dic = {"key":1,"key2":2}
for i in dic:
    dic.pop(i)
print(dic)
```

运行后报错：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day07/01 exercise.py", line 88, in <module>
    for i in dic:
RuntimeError: dictionary changed size during iteration
```

同样地，我们也可以通过复制一份字典，然后通过迭代新的字典来删除原字典中的键值对：

```python 
dic = {"key":1,"key2":2}
for i in dic.copy():
    dic.pop(i)
print(dic)
```



[^1]: 应对方法不止这两种，可以任意开发