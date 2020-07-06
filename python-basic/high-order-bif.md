## 高阶内置函数

[TOC]

这一部分主要是一些 Python 中内置的高阶函数。所谓的高阶函数，就是以函数为参数的函数。

### filter 函数

filter 函数用来过滤掉不符合条件的元素。filter 函数有两个参数，第一个参数为规则函数，第二个参数为可迭代对象：

```python
lst = [1, 2, 3, 4, 5, 6, 7, 8]
def foo(x):            # 规则函数
    return x > 4       # 规则函数的返回值需要是布尔值
print(filter(foo, lst))
print(list(filter(foo, lst)))
```

输出的结果为：

```python
<filter object at 0x000002B53EA8AB00>
[5, 6, 7, 8]
```

filter 的返回值为 filter 对象，可迭代，可以通过 list 函数转化为列表。

我们可以通过 for 循环模拟内置函数 filter：

```python
def filter(func, iter):
    lst = []
    for i in iter:
        if func(i):
            lst.append(i)
    return lst
```

也可以使用匿名函数作为规则函数，这样可以让代码看起来非常简洁：

```python
# 找到年纪大于16岁的人的信息
lst = [{'id': 1,'name': 'alex','age': 18},
        {'id': 1,'name': 'wusir','age': 17},
        {'id': 1,'name': 'taibai','age': 16},]
print(list(filter(lambda x: x['age'] > 16, lst)))    # 过滤条件
```

输出的结果为：

```python
[{'id': 1, 'name': 'alex', 'age': 18}, {'id': 1, 'name': 'wusir', 'age': 17}]
```

### map 函数

map 函数也称作映射函数，用来将可迭代对象中每个元素执行函数功能：

```python
lst = [1, 2, 3, 4, 5, 6, 8, 9]
print(map(str, lst))
print(list(map(str, lst)))
```

输出的结果为：
```python
<map object at 0x000001ABD67EA908>
['1', '2', '3', '4', '5', '6', '8', '9']
```

map 函数返回的是 map 对象，也可以使用 list 函数转换为列表。

map 函数可以使用更多参数的规则函数来整合多个可迭代对象：

```python
lst1 = [1,2,3]
lst2 = [3,2,1]
lst3 = [3,2,1,5]
print(list(map(lambda x, y, z: x + y + z, lst1, lst2, lst3)))

输出的结果为：
[7, 6, 5]
```

如果可迭代对象长度不同，map 函数的迭代次数以最短的可迭代对象为准。

### sorted 函数

sorted 函数用来将可迭代对象排序：

```python
print(sorted("alex,mdsb"))  # 升序
print(sorted(('alex','mdsb'),reverse=True)) # 降序

dic = {1:'a',3:'c',2:'b'}
print(sorted(dic))
```

输出的结果为：

```python
[',', 'a', 'b', 'd', 'e', 'l', 'm', 's', 'x']
['mdsb', 'alex']
[1, 2, 3]
```

不管输入的可迭代对象是什么样的数据类型，sorted 函数的返回值都是一个列表。

sorted 函数也可以使用规则函数，只是这次规则函数要通过使用关键字参数的方式引入：

```python
lst = ['天龙八部', '西游记', '红楼梦', '三国演义']
print(sorted(lst,key=len))  # key= 排序规则(函数名)
print(sorted(lst,key=lambda x: len(x)))

lst = [{'id': 1,'name': 'alex','age': 18},
    {'id': 2,'name': 'wusir','age': 17},
    {'id': 3,'name': 'taibai','age': 16},]
print(sorted(lst,key=lambda x: x['age'],reverse=True))
```

输出的结果为：

```python
['西游记', '红楼梦', '天龙八部', '三国演义']
['西游记', '红楼梦', '天龙八部', '三国演义']
[{'id': 1, 'name': 'alex', 'age': 18}, {'id': 2, 'name': 'wusir', 'age': 17}, {'id': 3, 'name': 'taibai', 'age': 16}]
```

列表的 `.sort()` 方法是在列表所在的原地进行修改，而 sorted 函数则是新建一个列表：

```python
lst = [1, 2, 3, 4, 65, -7]
print(sorted(lst))  # 新建列表
print(lst)

lst1 = [1, 2, 3, 4, 65, -7]
print(lst1.sort())  # 新建列表
print(lst1)
```

输出的结果为：

```python
[-7, 1, 2, 3, 4, 65]
[1, 2, 3, 4, 65, -7]
None
[-7, 1, 2, 3, 4, 65]
```

### max 和 min 函数

max 和 min 函数用来选取可迭代对象中的最大值或最小值，可以指定规则函数进行更复杂的选择：

```python
lst = [1, 2, 3, 4, 5, 6, -9, 10, -22]
print(max(lst))
print(min(lst, key=abs))
print(max(lst, key=lambda x: pow(x, 4) - pow(x, 2) + x))
```

输出的结果为：

```python
10
1
-22
```

也可以通过这两个函数找到最大的值或者最小的值对应的键：

```python
dic = {'a': 3, 'b': 2, 'c': 1}
print(max(dic.values()))
print(min(dic, key=lambda x: dic[x]))
```

输出的结果为：

```python
3
c
```

### reduce 函数

reduce 函数用来进行累运算。规则函数中会有两个参数，第一个参数用来存储上一次运算的结果，第二个参数传入下一个值，返回值为运算操作。

需要注意的是，在 Python 2 中，reduce 可以直接使用，而在 Python 3 中，需要在 functools 里面导入 reduce 函数：

```python
from functools import reduce
```

我们可以通过 reduce 函数实现累乘运算：

```python
from functools import reduce
def func(x, y):
    return x * y
print(reduce(func, range(1, 6)))

输出的结果为： 120
```

将函数用匿名函数整合会让代码更加简洁：

```python
from functools import reduce
print(reduce(lambda x, y: x * y, range(1, 6)))
```

### zip 函数

zip 是拉链的意思，用来将两个可迭代对象以关联起来，以返回值为 zip 对象，可以转换为列表，列表中的每个元素为原来的可迭代对象中的元素组成的元组：

```python
lst1 = [1, 2, 3, 4, 5]
lst2 = [5, 4, 3, 2, 1]
print(zip(lst1, lst2))
print(list(zip(lst1, lst2)))
```

返回的结果为：

```python
<zip object at 0x0000021A6A792B48>
[(1, 5), (2, 4), (3, 3), (4, 2), (5, 1)]
```

使用 map 函数也可以实现类似的功能：

```python
lst1 = [1, 2, 3, 4, 5]
lst2 = [5, 4, 3, 2, 1]
print(list(map(lambda x, y: (x, y), lst1, lst2)))
```

输出的结果为：

```python
[(1, 5), (2, 4), (3, 3), (4, 2), (5, 1)]
```

这种数据类型可以直接使用 dict 函数转换为字典：

```python
lst1 = [1, 2, 3, 4, 5]
lst2 = [5, 4, 3, 2, 1]
print(dict(zip(lst1, lst2)))
```

输出的结果为：

```python
{1: 5, 2: 4, 3: 3, 4: 2, 5: 1}
```

### 高阶内置函数比较

|            函数名            |  规则函数位置   | 规则函数形参数目 |       返回值数据类型        |
| :--------------------------: | :-------------: | :--------------: | :-------------------------: |
|    [filter](#filter 函数)    |      首位       |       1个        |  filter对象，可转换为列表   |
|       [map](#map 函数)       |      首位       |    1个或多个     |    map对象，可转换为列表    |
|    [reduce](#reduce 函数)    |      首位       |       2个        |     可迭代对象中的元素      |
| [max和min](#max 和 min 函数) | 末尾，用key指明 |       1个        |     可迭代对象中的元素      |
|    [sorted](#sorted 函数)    | 末尾，用key指明 |       1个        |            列表             |
|       [zip](#zip 函数)       |       无        |        无        | zip对象，可转换为列表或字典 |

