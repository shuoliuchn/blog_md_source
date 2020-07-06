# UnboundLocalError

## local variable 'b' referenced before assignment

### 1.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day10/exercise.py", line 227, in <module>
    print(func())
  File "C:/Users/Sure/PyProject/day10/exercise.py", line 225, in func
    b += 1
UnboundLocalError: local variable 'b' referenced before assignment
```

错误代码：

```python
b = 100
def func():
    b += 1
    return b
print(func())
```

错误原因：

在局部空间修改全局变量，但是没有声明。

解决方法：

不建议在局部空间修改全局变量。如果一定要修改，使用global声明。

```python
b = 100
def func():
    global b
    b = b + 1
    return b
print(func())
```

### 1.2

报错信息：

```python
Traceback (most recent call last):
  File Assignment, line 249, in <module>
    func()
  File Assignment, line 247, in func
    print(name)
UnboundLocalError: local variable 'name' referenced before assignment
```

错误代码：

```python
name = '宝元'
def func():
    print(name)
    name = 'alex'
func()
```

错误原因：

在局部使用一次使用`name`这个变量时，局部不存在该变量，需要去全局寻找。此时局部已经认定`name`为全局变量。后面又在局部对`name`赋值，也就是试图在局部空间修改全局变量，却没有事先将变量声明为全局变量，所以会报错。

解决方法：

1. 如果目的是要将全局变量中的`name`修改，那就先声明其为全局变量

```python
name = '宝元'
def func():
    global
    print(name)
    name = 'alex'
func()
```

2. 如果目的是想要在局部创建一个值为`alex`的新变量，那就换一个变量名，不要与全局变量冲突

```python
name = '宝元'
def func():
    global
    print(name)
    name1 = 'alex'
func()
```

