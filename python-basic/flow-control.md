## 流程控制语句

[TOC]

流程控制语句，也就是条件语句，通过选择判断，决定下一步的操作内容。例如：如果是男的，就来看我。

流程控制语句的关键字是 `if`，是 `如果` 的意思。流程控制语句使用冒号 `:` 表示语句结束。

Python 中使用缩进体现代码间的从属关系。一般使用四个空格或一个 `Tab` 键代表一次缩进。需要注意的是，编程时，不要混合使用 `Tab` 键和空格，否则一旦报错，很难找到问题的所在。PEP 8 规则建议使用四个空格表示缩进。PEP 8 规则是 Python 官方建议的代码风格指南。若想进一步了解这个规则可以参见我翻译的 [PEP 8 -- Python代码格式规则](..\translation\PEP8.md)。

知识点补充：在 Python 中，`=` 表示的是 `赋值` **操作**，会将等号右边的值赋值给等号左边的变量。而 `==` 表示的是**判断**两边的值是否相等，也就是 `等于` 的意思。

### 单 if

单 `if` 流程控制语句的伪代码格式为：

```python
如果 条件:
缩进 结果
```

具体示例如下：

```python 
sex = "男"
if sex == "男":
    print("就来看我")
print(sex)
```

输出的结果为：

```python
就来看我
男
```

### if else 二选一

`if else` 二选一流程控制语句的伪代码格式为：

```python
如果 条件:
缩进 结果
否则:
缩进 结果
```

具体示例如下：

```python
print(111)
if 3 > 2:
    print(11)
    print(22)
else:
    print(333)
print(444)
```

打印出的结果为：

```python
111
11
22
444
```



### if elif elif 多选一或零

对于 `if elif elif` 多选一或零流程控制语句而言，只要其中任何一个条件成立，将执行其从属语句中的代码，而其他的语句将不被执行。其伪代码格式为：

```python
如果 条件:
缩进 结果
再如果 条件:
缩进 结果
再如果 条件:
缩进 结果
再如果 条件:
缩进 结果
```

具体示例如下：

```python
if 3>5:
    print(1)
elif 3>7:
    print(2)
elif 5>2:
    print(4)
elif 3>1:
    print(5)

```

输出结果为：

```python
4
```

### if elif elif else 多选一

`if elif elif else` 多选一流程控制语句的伪代码格式为：

```python
如果 条件:
缩进 结果
再如果 条件:
缩进 结果
再如果 条件:
缩进 结果
否则:
缩进 结果
```

具体示例如下：

```python
if 3>12:
   print(1)
elif 3>11:
    print(2)
elif 4>12:
    print(3)
else:
    print(5)
```

输出的结果为：

```python
5
```

### if 嵌套:

`if ` 嵌套流程控制语句的伪代码格式为：

```python
如果 条件:
缩进 如果 条件:
     缩进 结果
```

具体示例如下：

```python
sex = "男"
age = 48
if sex == "女":
    if age == 18:
        print("进来坐坐")
    else:
        print("隔壁找三哥")
else:
    print("去对门找alex")
```

输出结果为：

```python
去对门找alex
```

### if if if  多选多

`if if if ` 多选多流程控制语句的伪代码格式为：

```python
如果 条件:
缩进 结果
如果 条件:
缩进 结果
如果 条件:
缩进 结果
```

具体示例如下：

```python
if 43 > 1:
    print(11)
if 43 > 2:
    print(11)
if 43 > 3:
    print(11)
```

输出的结果为：

```python
11
11
11
```

补充内容：`and` 是 `和` 的意思。只有当 `and` 两端的值都为 `真` 时，会返回 `True`，否则都会返回 `False`：

```python
user = input("username:")
pwd = input("password:")
# and 是和
if user == "alex" and pwd == "alex123":
    print(111)
```

