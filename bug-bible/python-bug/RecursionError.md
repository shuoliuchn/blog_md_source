# RecursionError

## maximum recursion depth exceeded while calling a Python object

### 1.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day14/exercise.py", line 198, in <module>
    func()
  File "C:/Users/Sure/PyProject/day14/exercise.py", line 197, in func
    func()
  File "C:/Users/Sure/PyProject/day14/exercise.py", line 197, in func
    func()
  File "C:/Users/Sure/PyProject/day14/exercise.py", line 197, in func
    func()
  [Previous line repeated 994 more times]
  File "C:/Users/Sure/PyProject/day14/exercise.py", line 196, in func
    print(1)
RecursionError: maximum recursion depth exceeded while calling a Python object
```

错误代码：

```python
def func():
    print(1)
    func()
func()
```

错误原因：

递归没有明确的结束条件成为，死敌归。当函数执行到最大深度（官方默认设置为1000）时，就会报错强制停止。

解决方法：

给递归设置明确终止条件。

```python
def func(n):
    print(n)
    if n == 1:
        return n
    func(n - 1)
func(5)
```

