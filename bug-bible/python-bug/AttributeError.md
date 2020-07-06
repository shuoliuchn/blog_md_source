# AttributeError

## 'NoneType' object has no attribute '__closure__'

### 错误1.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/day13/exercise.py", line 100, in <module>
    f.__closure__
AttributeError: 'NoneType' object has no attribute '__closure__'
```

错误代码：

```python
def func():
    a = 10
    def foo():
        print(a)
f = func()
f.__closure__
```

错误原因：

测试闭包的时候，没有在外层函数中将内层函数返回就强行调用，所以报错。

解决方法：

把测试闭包的方法放到函数里面，不要忘记在外面调用函数，否则函数中的代码是不会被运行的。

```python
def func():
    a = 10
    def foo():
        print(a)
    print(foo.__closure__)
func()
```

补充：

外层函数中不将内层函数返回也可以构成闭包，但是这个闭包无法被使用。

## module 'bake.api' has no attribute 'policy'

### 错误2.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/bake/test.py", line 21, in <module>
    print(api.policy.name)
AttributeError: module 'bake.api' has no attribute 'policy'
```

错误代码：

```python
from bake import api
print(api.policy.name)
```

错误原因：

其实这两行代码没有问题。只是没有在`api`包的`__init__.py`文件中指明需要导入的方法。当调用`api`包时，找不到`policy`，所以报错。

解决方法：

在`api`包中的`__init__.py`文件中加入需要导入的函数名和变量名：

```python
from .policy import *
```

## 'Person' object has no attribute '__name'

### 错误3.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/week06/day22/exercise.py", line 7, in <module>
    print(p1.__name)
AttributeError: 'Person' object has no attribute '__name'
```

错误代码：

```python
class Person:
    def __init__(self, name):
        self.__name = name
p1 = Person('迪丽热巴')
print(p1.__name)
```

错误原因：

以`__`开头的属性或方法是类的私有属性或私有方法，不能被实例对象查看或修改。若想修改或查看，只能通过在类中创建一个方法来实现

解决方法：

创建两个方法，分别查看和修改`__name`变量

```python
class Person:
    def __init__(self, name):
        self.__name = name
    def get_name(self):
        return self.__name
    def set_name(self, name):
        self.__name = name

p1 = Person('迪丽热巴')
print(p1.get_name())
p1.set_name('唐艺昕')
print(p1.get_name())
```

补充：

在Python中，私有变量其实是更改了名字的实例变量，新名字为`_类名属性名`。例如，上面的例子中，`__name`变量其实在对象中的存在形式为`_Person__name`，我们也可以通过它的新名字实现对私有属性的查看和修改，但是并不推荐这种做法：

```python
class Person:
    def __init__(self, name):
        self.__name = name

p1 = Person('迪丽热巴')
print(p1._Person__name)
p1._Person__name = '刘亦菲'
print(p1._Person__name)
```

## `__enter__`

### 错误3.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/week08/day32/exercise.py", line 46, in <module>
    with A('大爷') as a:
AttributeError: __enter__
```

错误代码：

```python
class A:
    def __init__(self, text):
        self.text = text
with A('大爷') as a:
    print(a)
```

错误原因：

使用with语句时，类中需要有`__enter__`和`__exit__`方法。

解决方法：

重写两个方法。

```python
class A:
    def __init__(self, text):
        self.text = text
    def __enter__(self):
        print(f'我是你{self.text}')
        return self
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.text += '这就走啦~'

with A('大爷') as a:
    print(a)
    print(a.text)
print(a.text)
```

