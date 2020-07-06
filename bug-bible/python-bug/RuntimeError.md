# RuntimeError

## dictionary changed size during iteration

### 错误1.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day07/01 exercise.py", line 88, in <module>
    for i in dic:
RuntimeError: dictionary changed size during iteration
```

错误代码：

```python
dic = {"key":1,"key2":2}
for i in dic:
    dic.pop(i)
print(dic)
```

错误原因：

迭代过程中删除键值对。字典和集合在迭代过程中如果发生长度变化会报错。

解决方法：

用其他方法进行删除操作，比如复制一个字典，循环新复制的字典尽心删除

```python
dic = {"key":1,"key2":2}
for i in dic.copy():
    dic.pop(i)
print(dic)
```

