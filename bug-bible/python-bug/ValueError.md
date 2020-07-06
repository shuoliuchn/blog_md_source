# ValueError

## not enough values to unpack (expected 2, got 1)

### 错误1.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day08/作业/05 文件转列表字典.py", line 16, in <module>
    k, v = i.split()
ValueError: not enough values to unpack (expected 2, got 1)
```

错误代码：

```python
l = []
with open('a2.txt', 'r', encoding='utf-8') as f:
    for line in f:
        lst = line.split()
        kv = []
        for i in lst:
            k, v = i.split()    # 这里错了
            if v.isdecimal():
                v = int(v)
            kv.append((k, v))
        dic = dict(kv)
        l.append(dic)
print(l)
```

错误原因：

使用`.split()`方法时，需要确定字符串中包含标记位置的元素。上面的代码本意是想要通过`':'`进行切割，但是忘记了加。`.split()`方法默认按照空格、换行符和制表符切割，但是字符串中又没有相应的标记，故而报错。

解决方法：

在`.split()`方法中加入参数，将切割位置设定在`':'`。

```python
l = []
with open('a2.txt', 'r', encoding='utf-8') as f:
    for line in f:
        lst = line.split()
        kv = []
        for i in lst:
            k, v = i.split(':')
            if v.isdecimal():
                v = int(v)
            kv.append((k, v))
        dic = dict(kv)
        l.append(dic)
print(l)
```

## I/O operation on closed file.

### 错误2.1

报错信息：

```python
Traceback (most recent call last):
  File 第二周大作业-博客园.py, line 91, in <module>
    dic[choose]()
  File 第二周大作业-博客园.py, line 23, in register
    f.write(f"{j['name']} {j['pwd']} {j['count']}\n")
ValueError: I/O operation on closed file.
```

错误代码：

```python
with open('temp', 'w', encoding='utf-8') as f1:
    for j in user_info:
        f.write(f"{j['name']} {j['pwd']} {j['count']}\n")
        f.flush()
```

错误原因：

文件句柄的名字是f1，但是写入文件时使用的是f。PyCharm中没有飘红，是因为之前有用到过f作为文件句柄。这也是问什么报错信息是该文件已关闭。

解决方法：

把要操作的文件句柄改回f1即可。

```python
with open('temp', 'w', encoding='utf-8') as f1:
    for j in user_info:
        f1.write(f"{j['name']} {j['pwd']} {j['count']}\n")
        f1.flush()
```

## substring not found

### 错误3.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/week05-国庆假期/test.py", line 6, in <module>
    print(s.index('ll'))
ValueError: substring not found
```

错误代码：

```python
s = 'alex'
print(s.index('le'))
print(s.index('ll'))
```

错误原因：

使用`.index()`方法查找的内容`ll`再原字符串中不存在，所以报错。

解决方法：

使用`.index()`时，需要确定查找的内容在原字符串中存在。如果不能确定查找内容是否存在于原字符串中，可以使用`.find()`方法。与`.index()`方法不同的是，`.find()`方法当查找不到相应内容时，会返回`-1`，而不是报错。

```python
s = 'alex'
print(s.index('le'))
print(s.find('ll'))
```

## too many values to unpack

### 错误4.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/神器/hexo-migration/bin/run.py", line 3, in <module>
    src.run()
  File "C:\Users\Sure\PyProject\神器\hexo-migration\core\src.py", line 16, in run
    migration_handler.migrate()
  File "C:\Users\Sure\PyProject\神器\hexo-migration\utils\MigrationHandler.py", line 152, in migrate
    self.file_write()
  File "C:\Users\Sure\PyProject\神器\hexo-migration\utils\MigrationHandler.py", line 54, in file_write
    self.yaml_front_matter_generator()
  File "C:\Users\Sure\PyProject\神器\hexo-migration\utils\MigrationHandler.py", line 23, in yaml_front_matter_generator
    for k, v in self.title_dict:
ValueError: too many values to unpack (expected 2)
```

错误代码：

```python
for k, v in self.title_dict:
    yaml_str = yaml_str + k + ': ' + v + '\n'
```

错误原因：

若要同时获取字典中的键和值，需要使用字典的 items 方法，而不是直接使用。不要和 vue 搞混了。

解决方法：

在字典的后面加上 `.items()`，使用 items 方法获取键和值

```python
for k, v in self.title_dict.items():
    yaml_str = yaml_str + k + ': ' + v + '\n'
```

