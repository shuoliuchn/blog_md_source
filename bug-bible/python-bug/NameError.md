# NameError

## name 'alex' is not defined
### 错误1.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day01/first_repeat/02 变量.py", line 53, in <module>
​    你好 = alex
NameError: name 'alex' is not defined
```

错误代码：

```python
你好 = alex
print(你好)
```

错误原因：

字符串连边没有加引号声明。没有加引号的一串字符在python中是变量而不是字符串。

解决方法：

在字符串两端加上引号

```python
你好 = 'alex'
print(你好)
```

附注：

这里面还有一个不恰当的操作是，一般不使用中文和拼音作为变量名。

## name 'lst' is not defined

### 错误2.1

这个错误和name 'dic' is not defined的[错误3.1](#错误3.1)相同

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day04/02 exercise.py", line 31, in <module>
    print(lst)
NameError: name 'lst' is not defined
```

错误代码：

```python
lst = ['大圣', '海芋', '新力', '一帆', '靓仔', '石淦']
del lst
print(lst)
```

错误原因：

使用`del`将列表`lst`删除。此时`lst`已经不存在。然后`print`调用`lst`，系统找不到而报错。

解决方法：

不适用`del`方法或者删除后不调用，或者通过加入索引值只删除列表中的某个元素，而不是整个删除。

```python
lst = ['大圣', '海芋', '新力', '一帆', '靓仔', '石淦']
del lst[2]
print(lst)
```

## name 'dic' is not defined

### 错误3.1

这个错误和name 'lst' is not defined的[错误2.1](#错误2.1)相同

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day05/01 exercise.py", line 80, in <module>
    print(dic)
NameError: name 'dic' is not defined
```

错误代码：

```python
dic = {'key': 1, 'dsb': 'alex'}
del dic
print(dic)
```

错误原因：

使用`del`将字典`dic`整个删除。此时`dic`已经不存在。然后`print`调用`dic`，系统找不到而报错。

解决方法：

不适用`del`方法或者删除后不调用，或者通过指定的键删除字典中指定的键值对，而不是整个删除。

```python
dic = {'key': 1, 'dsb': 'alex'}
del dic['dsb']
print(dic)
```

## name 'utf' is not defined

### 错误4.1

报错信息：

```python
Traceback (most recent call last):
  File Assignment, line 4, in <module>
    s1 = s1.encode(utf-8)
NameError: name 'utf' is not defined
```

错误代码：

```python
s1 = '宝元'
s1 = s1.encode(utf-8)
print(s1)
```

错误原因：

编码方法需要加引号，以字符串的形式传入。不加引号的意思是变量。

解决方法：

在utf-8外面加上引号

```python
s1 = '宝元'
s1 = s1.encode('utf-8')
print(s1)
```

