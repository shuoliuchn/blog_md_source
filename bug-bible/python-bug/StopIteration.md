# StopIteration



### 错误1.1

报错信息：

```python
  File "C:/Users/Sure/PyProject/week03/day11/exercise.py", line 59, in <module>
    print(l.__next__())
StopIteration
```

错误代码：

```python
lst = [1, 2, 3, 4, 5]
l = lst.__iter__()
print(l)
print(l.__next__())
print(l.__next__())
print(l.__next__())
print(l.__next__())
print(l.__next__())
print(l.__next__())
```

错误原因：

迭代器中有多少个元素，就只能使用多少次`.__next__()`方法，如果次数超过元素个数，会报错。

解决方法：

减少使用`.__next__()`的次数，或者创建新的迭代器。

```python
lst = [1, 2, 3, 4, 5]
l = lst.__iter__()
print(l)
print(l.__next__())
print(l.__next__())
print(l.__next__())
print(l.__next__())
print(l.__next__())
```