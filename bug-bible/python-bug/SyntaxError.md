# SyntaxError

## invalid syntax
### 错误1.1

这个错误和invalid token的[错误2.1](#错误2.1)相同

报错信息：

```python
 File "C:/Users/Sure/PyProject/day01/first_repeat/02 变量.py", line 36
    1a = '刘硕'
     ^
SyntaxError: invalid syntax
```

错误代码：

```python
1a = '刘硕'
print(1a)
```

错误原因：

变量名不可以以数字开头

解决方法：

重新命名变量即可

```python
a1 = '刘硕'
print(a1)
```

附，python中变量的命名规范：

1. 数字、字母和下划线组成

2. 不能以数字开头

3. 禁止使用python中的关键字

4. 变量名要具有可描述性

5. 变量名要区分大小写

6. 不能使用中文和拼音

7. 推荐写法：

8. 1. 驼峰体
   2. 下划线

### 错误1.2

报错信息：

```python
 File "C:/Users/Sure/PyProject/day01/first_repeat/05 用户交互.py", line 12
    num int(input('请输入数字：'))
          ^
SyntaxError: invalid syntax
```

错误代码：

```python
num int(input('请输入数字：'))
print(num + 5)
```

错误原因：

忘记在变量和input之间加赋值号

解决方法：

把赋值号补上

```python
num = int(input('请输入数字：'))
print(num + 5)
```

### 错误1.3

报错信息：

```python
  File Assignment, line 16
    print('用户名或密码错误，请重新输入！剩余尝试机会：%s次' % count += 1)
                                               ^
SyntaxError: invalid syntax
```

错误代码：

```python
print('用户名或密码错误，请重新输入！剩余尝试机会：%s次' % count += 1)
```

错误原因：

Python中，赋值操作不能和调用操作同时使用。

解决方法：

将复制和调用分开，先赋值，后调用

```python
count += 1
print('用户名或密码错误，请重新输入！剩余尝试机会：%s次' % count)
```

### 错误1.4

报错信息：

```python
  File "C:/Users/Sure/PyProject/day04/作业/07 3整除列表.py", line 4
    for i in range 51:
                    ^
SyntaxError: invalid syntax
```

错误代码：

```python
for i in range 51:
```

错误原因：

使用range时没有加括号

解决方法：

加上括号

```python
for i in range(51):
```

### 错误1.5

报错信息：

```python
  File "C:/Users/Sure/PyProject/week06/day20/exercise.py", line 127
    delattr(GirlFriend, 'sex')
          ^
SyntaxError: invalid syntax
```

错误代码：

```python
xiaoli = GirlFriend('小丽', 16)
xiaohong = GirlFriend('小红', 17)
del xiaoli.age
delattr(xiaohong, 'age')
print(xiaoli.__dict__)
print(xiaohong.__dict__)

print(GirlFriend.__dict__.get('sex', '没有找到~')
delattr(GirlFriend, 'sex')
print(GirlFriend.__dict__.get('sex', '没有找到~')
```

错误原因：

del和delattr方法能力有限，只能删除实例属性，不能删除类属性

解决方法：

不删除类属性

```python
xiaoli = GirlFriend('小丽', 16)
xiaohong = GirlFriend('小红', 17)
del xiaoli.age
delattr(xiaohong, 'age')
print(xiaoli.__dict__)
print(xiaohong.__dict__)
```



## invalid token

### 错误2.1

这个错误和invalid syntax的[错误1.1](#错误1.1)相同

报错信息：

```python
  File "C:/Users/Sure/PyProject/day01/first_repeat/02 变量.py", line 71
    1_a = '三哥'
     ^
SyntaxError: invalid token
```

错误代码：

```python
a1 = 'alex'
1_a = '三哥'
a_1_a = 'goub'

print(a1)
print(a_1_a)
print(1_a)
```

错误原因：

变量名不可以以数字开头

解决方法：

重新命名变量即可

```python
a1 = 'alex'
a_1 = '三哥'
a_1_a = 'goub'

print(a1)
print(a_1_a)
print(a_1)
```

## no binding for nonlocal 'a' found

### 错误3.1

报错信息：

```python
  File "C:/Users/Sure/PyProject/day10/exercise.py", line 268
    nonlocal a
    ^
SyntaxError: no binding for nonlocal 'a' found
```

错误代码：

```python
a = 15
def func():
    def foo():
        nonlocal a
        a = a + 1
    foo()
func()
print(a)
```

错误原因：

使用nonlocal声明变量名为更高级的全局变量时，如果外层函数中没有相关的变量，即便在全局中有也不行，程序会报错而不是创建局部变量。

解决方法：

使用nonlocal方法时，先确定外层函数中存在声明的变量

```python
a = 15
def func():
    a = 10
    def foo():
        nonlocal a
        a = a + 1
    foo()
func()
print(a)
```

## (unicode error) 'unicodeescape' codec can't decode bytes in position 2-3: truncated \UXXXXXXXX escape

### 错误4.1

报错信息：

```python
  File "C:/Users/Sure/PyProject/exercise.py", line 2
    sys.path.append('C:\Users\Sure\Desktop')
                   ^
SyntaxError: (unicode error) 'unicodeescape' codec can't decode bytes in position 2-3: truncated \UXXXXXXXX escape
```

错误代码：

```python
import sys
sys.path.append('C:\Users\Sure\Desktop')
from test1 import a
print(a)
```

错误原因：

路径中的\U有特殊含义，在字符串中造成歧义。

解决方法：

在路径字符串前加入一个r

```python
import sys
sys.path.append(r'C:\Users\Sure\Desktop')
from test1 import a
print(a)
```

