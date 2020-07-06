# TypeError

## '>=' not supported between instances of 'str' and 'int'

### 错误1.1

这个错误和must be str, not int的[错误3.1](#错误3.1)相同

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day01/Homework/11 成绩档次.py", line 3, in <module>
    if score >= 90:
TypeError: '>=' not supported between instances of 'str' and 'int'
```

错误代码：

```python
score = input('请输入你的成绩：')
if score >= 90:
print('你的成绩是A')
elif score >= 80:
print('你的成绩是B')
elif score >= 70:
print('你的成绩是C')
elif score >= 60:
print('你的成绩是D')
else:
print('你的成绩是不及格')
```

错误原因：

Python 3中，input获取的内容全都是字符串。字符串和整型数据之间是不可以进行比较大小的。

解决方法：

用int函数将输入的内容转化成整型数据即可

```python
score = int(input('请输入你的成绩：'))
if score >= 90:
print('你的成绩是A')
elif score >= 80:
print('你的成绩是B')
elif score >= 70:
print('你的成绩是C')
elif score >= 60:
print('你的成绩是D')
else:
print('你的成绩是不及格')
```

## 'str' object is not callable

### 错误2.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day01/first_repeat/02 变量.py", line 70, in <module>
​    print(print)
TypeError: 'str' object is not callable
```

错误代码：

```python
Print = 'alex'
print(print)
```

错误原因：

把变量命名为python中的关键字（print）

解决方法：

重新命名变量即可

```python
name = 'alex'
print(name)
```

附，python中的关键字：

```python
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'returen', 'try', 'while', 'with', 'yield']
```

## must be str, not int

###  错误3.1

这个错误和'>=' not supported between instances of 'str' and 'int'的[错误1.1](#错误1.1)相同

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day01/first_repeat/05 用户交互.py", line 10, in <module>
​    print(num + 5)
TypeError: must be str, not int
```

错误代码：

```python
num = input('请输入数字：')
print(num + 5)
```

错误原因：

Python 3中，input获取的内容全都是字符串。字符串和整型数据之间是不可以进行加和操作的。

解决方法：

用int函数将输入的内容转化成整型数据即可

```python
num = int(input('请输入数字：'))
print(num + 5)
```

## unhashable type: 'dict'

### 错误4.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day05/01 exercise.py", line 54, in <module>
    dic = {{'a': 1}: 'alex'}
TypeError: unhashable type: 'dict'
```

错误代码：

```python
dic = {{'a': 1}: 'alex'}
print(dic)
```

错误原因：

字典的键需要是可哈希的数据类型。字典是可变数据类型，不可哈希，所以报错。

解决方法：

不用字典和其他不可哈希的数据作为字典的键

```python
dic = {'alex': {'a': 1}}
print(dic)
```

附可哈希的数据类型：

1. 整型
2. 字符串
3. 布尔值
4. 元组

不可哈希的数据类型：

1. 列表
2. 字典

## unhashable type: 'slice'

### 错误5.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day05/01 exercise.py", line 58, in <module>
    dic["alex": 89]
TypeError: unhashable type: 'slice'
```

错误代码：

```python
dic = {"key": 1}
dic["alex": 89]
print(dic)
```

错误原因：

字典增加操作的输入不规范，正确格式为：`字典["键"] = "值"`。

解决方法：

修改为正确格式

```python
dic = {'alex': {'a': 1}}
print(dic)
```

## an integer is required (got type str)

### 错误6.1

报错信息：

```python
Traceback (most recent call last):
  File Assignment, line 10, in <module>
    with open('t1.txt', 'r', 'utf-8') as f:
TypeError: an integer is required (got type str)
```

错误代码：

```python
with open('t1.txt', 'r', 'utf-8') as f:
    for line in f:
        print(line)
```

错误原因：

文件打开操作时，编码方式需要指定参数名为`encoding`

解决方法：

在编码方式utf-8前指明参数名。

```python
with open('t1.txt', 'r', encoding='utf-8') as f:
    for line in f:
        print(line)
```

## 'builtin_function_or_method' object is not subscriptable

### 错误7.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day07/01 exercise.py", line 69, in <module>
    l.append[2]
TypeError: 'builtin_function_or_method' object is not subscriptable
```

错误代码：

```python
l = []
dic = dict.fromkeys('abc', l)
l.append[2]
print(l, dic)
```

错误原因：

方法的调用需要使用小括号而不是中括号，中括号是索引，小括号才是调用。

解决方法：

将中括号改为小括号。

```python
l = []
dic = dict.fromkeys('abc', l)
l.append(2)
print(l, dic)
```

## eat() missing 2 required keyword-only arguments: 'a' and 'b'

### 错误8.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day10/exercise.py", line 12, in <module>
    eat("面条","米饭","馒头","包子","煎饼")
TypeError: eat() missing 2 required keyword-only arguments: 'a' and 'b'
```

错误代码：

```python
def eat(*args,a,b):
    print(a,b,args)
eat("面条","米饭","馒头","包子","煎饼")
```

错误原因：

设置形参时，将动态位置参数放到位置参数前面，导致位置参数无法获得参数。

解决方法：

将动态位置参数放到位置参数后面。

```python
def eat(a,b,*args):  # 位置参数,动态位置参数
    print(a,b,args)
eat("面条","米饭","馒头","包子","煎饼")
```

## 'int' object is not callable

### 错误9.1

报错信息：

```python
Traceback (most recent call last):
  File Assignment, line 190, in <module>
    func()
  File Assignment, line 189, in func
    inner()
TypeError: 'int' object is not callable
```

错误代码：

```python
def func():
    item = 'alex'
    def inner():
        print(item)
    for inner in range(10):
        pass
    inner()
func()
```

错误原因：

`for`循环的本质是将可迭代对象迭代赋值给变量。因为程序自上而下运行，一旦变量名与函数名相同，处在下面的变量会覆盖函数名。当后来想要调用函数时，**变量**`inner`表示的不是**函数**`inner`的内存地址，而是`9`的内存地址。`9`是整数，当然是无法被调用的。

解决方法：

for循环使用新的变量名，不要与其他变量和函数相同。

```python
def func():
    item = 'alex'
    def inner():
        print(item)
    for i in range(10):
        pass
    inner()
func()
```

## 'NoneType' object is not callable

### 错误10.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day13/exercise.py", line 74, in <module>
    f(12000)
TypeError: 'NoneType' object is not callable
```

错误代码：

```python
def ave_turnover():
    lst = []
    def inner(turnover_today):
        lst.append(turnover_today)
        ave = sum(lst) / len(lst)
        print(ave)
        return inner
f = ave_turnover()
f(12000)
```

错误原因：

写闭包时`return`闭包函数的应该是外层函数，而不是内存函数返回自己。`return`的缩进应该和内层函数的`def`对齐，而不是在内层函数中。

解决方法：

将`return inner()`的缩进向外调节。

```python
def ave_turnover():
    lst = []
    def inner(turnover_today):
        lst.append(turnover_today)
        ave = sum(lst) / len(lst)
        print(ave)
    return inner
f = ave_turnover()
f(12000)
```

## unsupported operand type(s) for -: 'builtin_function_or_method' and 'builtin_function_or_method'

### 错误11.1

报错信息：

```python
Traceback (most recent call last):
i am in index
  File "C:/Users/Sure/PyProject/day13/exercise.py", line 162, in <module>
    print(time.time - start_time)
TypeError: unsupported operand type(s) for -: 'builtin_function_or_method' and 'builtin_function_or_method'
```

错误代码：

```python
import time

def index():
    time.sleep(0.5)  # 休眠0.5秒，阻塞函数
    print('i am in index')

def func():
    time.sleep(0.8)
    print('i am in func')

def foo():
    time.sleep(0.4)
    print('i am in func')

start_time = time.time
index()
print(time.time - start_time)

start_time = time.time
func()
print(time.time - start_time)

start_time = time.time
foo()
print(time.time - start_time)
```

错误原因：

`time.time`方法在使用的时候需要加括号。

解决方法：

把括号加上

```python
import time

def index():
    time.sleep(0.5)  # 休眠0.5秒，阻塞函数
    print('i am in index')

def func():
    time.sleep(0.8)
    print('i am in func')

def foo():
    time.sleep(0.4)
    print('i am in func')

start_time = time.time()
index()
print(time.time() - start_time)

start_time = time.time()
func()
print(time.time() - start_time)

start_time = time.time()
foo()
print(time.time() - start_time)
```

## 'generator' object is not subscriptable

### 错误12.1

报错信息：

```python
Traceback (most recent call last):
  File Assignment, line 5, in <module>
    print(v[0])     # 报错了，生成器不能使用索引
TypeError: 'generator' object is not subscriptable
```

错误代码：

```python
v = (lambda :x for x in range(10))
print(v)    # 生成器内存地址
print(v[0])     # 报错了，生成器不能使用索引
```

错误原因：

生成器对象不能使用索引。

解决方法：

使用`next()`函数使用生成器对象。

```python
v = (lambda :x for x in range(10))
print(v)    # 生成器内存地址
print(next(v))    # 匿名函数内存地址
```

## findall() missing 1 required positional argument: 'string'

### 错误13.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day17/exercise.py", line 18, in <module>
    print(re.findall('\Aa'))    # 从开头匹配
TypeError: findall() missing 1 required positional argument: 'string'
```

错误代码：

```python
s3 = 'alex'
print(re.findall('\Aa'))    # 从开头匹配
print(re.findall('^a'))
```

错误原因：

`re.findall()`方法需要两个参数，第一个参数是正则表达式，第二个参数是查找的字符串。但是只传入了一个参数，没有把字符串放进去。

解决方法：

传入字符串参数。

```python
s3 = 'alex'
print(re.findall('\Aa', s3))    # 从开头匹配
print(re.findall('^a', s3))
```

## not enough arguments for format string

### 错误14.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/week06/day22/exercise.py", line 115, in <module>
    xiaoming.game(xiaohong)
  File "C:/Users/Sure/PyProject/week06/day22/exercise.py", line 107, in game
    print('%s和%s快乐地玩耍' % self.name, girl.name)
TypeError: not enough arguments for format string
```

错误代码：

```python
print('%s和%s快乐地玩耍' % self.name, girl.name)
```

错误原因：

格式化字符串时，如果有多个位置要格式化，后面的数据要放到括号中

解决方法：

将数据用括号括起来。

```python
print('%s和%s快乐地玩耍' % (self.name, girl.name))
```

## `__str__` returned non-string (type int)

### 错误15.1

报错信息：

```python
<__main__.A object at 0x00000230E107B6D8>
  File "C:/Users/Sure/PyProject/week07/day24/exercise.py", line 55, in <module>
    print(b)
TypeError: __str__ returned non-string (type int)
```

错误代码：

```python
class A:
    pass
class B:
    def __str__(self):
        return 12
a = A()
b = B()
print(a)
print(b)
```

错误原因：

魔法方法`__str__`的返回值只能是字符串类型

解决方法：

将返回值改成字符串

```python
class A:
    pass
class B:
    def __str__(self):
        return '这时B对象'
a = A()
b = B()
print(a)
print(b)
```

补充：

类似地，`__len__`方法的返回值必须是整型数字且不能为负