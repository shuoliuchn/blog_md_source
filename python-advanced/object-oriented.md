## 面向对象初识

[TOC]

### 面向过程与面向对象

编程语言发展历程：

机器语言 --> 汇编语言 --> 高级语言

数学向左，上帝向右。数学家的思维总是倾向于将事物抽象化，寻求程序运行效率的最大化。但是正常人的思维赶不上数学家的思维。而且在硬件运行速度足够快，内存空间足够大的今天，已经不特别在意程序的运行速度。人们更在意的是程序的开发速度。面向对象的编程思想应运而生。

面向过程，编程思想更接近于计算机（数学逻辑）

- 事件为中心：分析出解决问题所需的步骤，然后用函数把这些步骤实现，并按顺序调用
- 执行效率高
- 代码复用性差，不利于扩展和维护

面向对象，编程思想更接近于人类

- 与面向对象相辅相成，在软件开发过程中，宏观上，用面向对象来把握事件间复杂的关系
- 代码复用性更高，更加简洁和宏观，易于扩展和维护，更利于程序设计
- 性能低于面向过程

举一个通俗一点的例子，我们想要开一家饭店，如果用面向过程的方法，我们就要自己租房子，买菜，接待客人，做菜，擦桌子，洗盘子等等。如果用面向对象的方法，租房子我们去找中介，买菜去找采购，做菜找厨师，接待客人招服务员等等。

需要指明的是，面向过程和面向对象不属于任何一门编程语言，它们只是两种不同的编程思想。

### 类和对象

类是对象的抽象，是一类事物的总称。类是对现实世界进行抽象之后所得到的结果，包含属性和方法，用于创建对象。

对象是类的具象，是一个具体的事物。通过类进行具象（实例化）之后所得到的一个具体的事物（一块真实存在于内存中的空间，通过引用来访问），用来完成具体工作。

先有类，通过类可以创建对象。

数据类型是数据的封装，函数是算法的封装。

类是数据和算法的封装，更高一级。

区分类和对象：

```python
狗    # 类
奥迪A8    # 类
电脑    # 类
我的电脑    # 对象
苹果    # 类
我现在坐的椅子    # 对象
男人    # 类
我的女朋友    # 对象
```

### 创建类

我们使用 `class` 关键字创建一个类，类名要求同变量命名规则类似，同时要求首字母要大写。创建类时，类名后可以不加括号，如果一定要加括号，记得在里面加上 object：

```python
# 方法一，类名后面加(object)
class GirlFriend(object):
    sex = '女'
    
# 方法二，类名后什么都不加
class GirlFriend:
    sex = '女'
```

使用伪代码来表示，我们可以这样创建一个类：

```python
class 类名(父类):
    属性
    方法
```

类中包含属性（特征）和方法（行为）。属性通常是变量，用来表示类是什么样的；方法通常是函数，用来体现类能干什么。

我们可以通过 `.__dict__` 方法查看类中的所有内容：

```python
class GirlFriend:
    name = '小丽'
    age = 18
    height = 172
    weight = 100
    money = 10000000000

    def chui_tui(self):
        print('给主子捶腿~')
    def give_money(self):
        print('给你100元')
        self.money -= 100

print(GirlFriend.__dict__)
```

输出的结果为：

```python
{'__module__': '__main__', 'name': '小丽', 'age': 18, 'height': 172, 'weight': 100, 'money': 10000000000, 'chui_tui': <function GirlFriend.chui_tui at 0x00000267C73BDB70>, 'give_money': <function GirlFriend.give_money at 0x00000267C73BDAE8>, '__dict__': <attribute '__dict__' of 'GirlFriend' objects>, '__weakref__': <attribute '__weakref__' of 'GirlFriend' objects>, '__doc__': None}
```

在上面的例子中，`name`、`age`、`height`、`weight`、`money` 都是类的属性。`chui_tui` 和 `give_money` 是类的方法。

### 创建对象

对象的创建和调用方法用伪代码表示是这样的：

```python
对象名 = 类名()    # 创建一个类对象

对象名.属性名    # 调用类属性
对象名.方法名()   # 调用类方法

对象名.属性名 = 属性值    # 为当前对象添加新属性
```

为当前对象添加新方法的方式比较繁琐，也不常用，仅作了解即可：

```python
import types    # 先导入types方法

class Person:    # 创建类
    def __init__(self, name):
        self.name = name

def eat(self):    # 需要添加的函数，注意要使用self作为第一个参数
    print('吃东西')

xiaoming = Person('小明')    # 创建一个对象
xiaoming.eat = types.MethodType(eat, xiaoming)    # MethodType方法有两个参数，第一个参数是要像对象中添加的方法，第二个参数是接受方法的对象
xiaoming.eat()    # 此时，对象中已经存在eat方法
```

### 类属性和实例属性

类属性

- 属于类
- 当前类的所有对象该属性的值都一样
- 类对象推荐直接用类名访问，虽然也可以使用对象名访问（不推荐）
- 改变类变量的值，会作用于该类所有的实例化对象
- 可以通过类名在类外添加新的类属性

具体示例为：

```python
class GirlFriend:
    name = '小丽'
    age = 18
    height = 172
    weight = 100
    money = 10000000000

    def chui_tui(self):
        print('给主子捶腿~')
    def give_money(self):
        print('给你100元')
        self.money -= 100
        
xiaoli = GirlFriend()
xiaohong = GirlFriend()
print(xiaoli.name, xiaohong.name, GirlFriend.name)
GirlFriend.age = 16
print(xiaoli.age, xiaohong.age, GirlFriend.age)
GirlFriend.color = 'white'
print(xiaoli.color, xiaohong.color, GirlFriend.color)
```

输出的结果为：

```python
小丽 小丽 小丽
16 16 16
white white white
```

实例属性

- 实例属性（实例变量）需要使用初始化方法来定义，用伪代码表示为：

  ```python
  class 类名:
      def __init__(self, name):    # self只能在类内使用，在类外使用则无效
          self.name = name    # 形参可以和示例变量名相同，也常这样写
  ```

- 属于对象

- 当前类的对象可以拥有各自的属性值

- 实例变量只能通过对象名访问，无法通过类名直接访问

- 实例属性不仅可以在 `__init__` 方法里添加，还可以在类的其他方法中添加

- 查询对象中所有属性的方法为：`对象.__dict__`

具体操作实例为：

```python
class GirlFriend:
    eye_num = 2
    sex = '女'
    
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def chui_tui(self, num):
        print(f'{self.name}给大哥捶{num}次腿')
        self.love = True
    
xiaoli = GirlFriend('小丽', 16)
xiaohong = GirlFriend('小红', 17)
xiaoli.chui_tui(10)
print(xiaoli.love)

输出的结果为：
小丽给大哥捶10次腿
True
```

类对象和实例对象有几点需要注意：

- Python 允许通过对象访问类变量，但无法通过对象修改变量的值
- 通过对象修改变量的值，不是在给 `变量赋值`，而是定义新的实例变量
- 实例变量可以和类变量同名，但是在这种情况下，使用类对象将无法调用示实例变量[^1]
- 修改一个对象的实例变量，既不会影响类变量的值，也不会影响其他对象的实例变量

上述说明用实例验证如下：

```python
class GirlFriend:
    name = '小丽丽'
    eye_num = 2
    sex = '女'
    
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def chui_tui(self, num):
        print(f'{self.name}给大哥捶{num}次腿')
        self.love = True
    
xiaoli = GirlFriend('小丽', 16)
xiaohong = GirlFriend('小红', 17)
print(xiaoli.sex)    # 女
print(GirlFriend.__dict__['sex'])    # 女
print(xiaoli.__dict__)    # {'name': '小丽', 'age': 16}
xiaoli.sex = '保密'
print(GirlFriend.__dict__['sex'])    # 女
print(xiaoli.__dict__)    # {'name': '小丽', 'age': 16, 'sex': '保密'}
print(xiaohong.name)    # 小红
xiaoli.name = '小莉'
print(GirlFriend.__dict__['name'])    # 小丽丽
print(xiaoli.__dict__['name'])    # 小莉
print(xiaohong.__dict__['name'])    # 小红
```

输出的结果为：

```python
女
女
{'name': '小丽', 'age': 16}
女
{'name': '小丽', 'age': 16, 'sex': '保密'}
小红
小丽丽
小莉
小红
```

### 系统类

Python 无处不对象。比如，我们创建列表就是在进行类的实例化和调用：

```python
list1 = list()    # Python中内置了一个list类，这里直接进行实例化
list1.append(1)    # 调用list类中的append方法
```



[^1]: 这一点可以理解为变量的查找是有顺寻的，优先查找实例变量。当实例变量不存在时，去查找类变量。