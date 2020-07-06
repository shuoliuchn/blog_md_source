# FileNotFoundError

## [Errno 2] No such file or directory: 'a'

### 错误1.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/week05-国庆假期/test.py", line 23, in <module>
    f = open('a', 'r', encoding='utf-8')
FileNotFoundError: [Errno 2] No such file or directory: 'a'
```

错误代码：

```python
f = open('a', 'r', encoding='utf-8')
```

错误原因：

使用`r`方法读取不存在的文件。`r`方法不会创建文件。

解决方法：

使用`r`方法打开文件时需要确保文件已经存在。如果不确定文件是否存在，尽量使用`a+`方法来读取文件。

```python
f = open('a', 'a+', encoding='utf-8')
```

