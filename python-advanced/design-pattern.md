## 常见设计模式

[TOC]

先想象一下这样一个场景：去麦当劳点餐，我们想吃麦辣鸡腿堡、薯条和可乐。我们当然可以单点，但是如果我们对收银小姐姐说，请给我一份麦辣鸡腿堡套餐，就会方便很多。套餐是餐厅和我们约定好了的一个规范，如果大家都按照这个规范来点餐，就能够提高点餐效率。

创建套餐是提高点餐效率的可重复使用的解决方法。套餐可以重复被点，而且提高了顾客和服务员之间的共同效率。

类似地，在编写程序的软件世界，对于统一情境，众多软件开发任意经过长时间总结出了最佳的可重用的解决方案，就是软件的设计模式。

软件世界本没有设计模式，开发人员多了，开发得久了，也便总结出了设计模式。

设计模式有很多很多种，对于不同的场景有不同的设计模式。这里只介绍两种非常常见的设计模式：单例模式和工厂模式。

### 单例模式

我们从前学习的类创建对象时，每个对象都是开辟相互独立的内存空间，它们各自的实例属性和方法是互无联系互不影响的。但是有的时候，我们只想创建一个对象，不管被实例化多少次，使用的对象都是一个。

比如在开发过程中，不同的程序员负责不同的模块。他们都需要使用到一个类对象，但是用了不同的名字。但是到后来当程序运行的时候，他们有需要这些对象都是同一个对象。这就需要用到单例模式。

单例模式是保证一个类仅有一个实例的设计模式。

单例模式保证了程序在不同位置都可以且尽可以取到同一个对象的实例：如果实例不存在，则会创建一个实例；如果对象存在，则会返回这个实力。

创建单例模式之前，我们要先讨论一下 `__new__()` 方法。同 `__init__()` 方法一样，`__new__()` 方法也是在创建对象时自动运行的。不同的是，`__init__()` 方法用来初始化对象，而 `__new()__` 方法用来创建对象。

换句话说，`__new__()` 方法在对象创建之前就会运行，它是一个类方法。它的返回值就是新创建的对象。

有了这些基础，结合我们从前学过的东西，就可以创建一个单例模式的类了：

```python
class Student:
    # 类属性，用来存放实例对象内存地址，同时也用来标记类是否已经被实例化，初始值为None
    instance = None

    # cls用来指代类，args和kwargs用来接收参数，PyCharm默认填入，不必理会也不必修改
    def __new__(cls, *args, **kwargs):
        # 如果没有实例化，就对其进行实例化操作，将实例化后的对象储存到类属性instance中
        if not cls.instance:
            # 这里的参数只放cls，不能放*args和**kwargs
            cls.instance = object.__new__(cls)
        # 不管开始的instance是否为空，此时它都已经储存了一个类对象，将其返回即可
        return cls.instance

    def __init__(self, name, age):
        self.name = name
        self.age = age


xiaoli = Student('小丽', 16)
xiaoming = Student('小明', 18)
print(xiaoli.name)
print(xiaoming.name)
```

输出结果为：

```python
小明
小明
```

需要注意的是，虽然不会创建新的实例对象，但是每当创建新对象时，初始化方法 `__init__()` 还是会执行的。因为是同一个对象，所以后面创建的实例属性就会覆盖从前创建的实例属性。

因为单例模式太过常用且重要，所以我们往往将其单独写成一个类。继承这个单例模式类的新类也都会是单例模式：

```python
class Singleton:
    instance = None
    def __new__(cls, *args, **kwargs):
        if not cls.instance:
            cls.instance = object.__new__(cls)
        return cls.instance

class Student(Singleton):
    def __init__(self, name, age):
        self.name = name
        self.age = age


xiaoli = Student('小丽', 16)
xiaoming = Student('小明', 18)
print(xiaoli.name)
print(xiaoming.name)
```

### 工厂模式

我们现在有这样一个需求：我们玩王者荣耀，想要一个物理伤害，射程远，可以持续输出的英雄。如果我们自己去找，很慢而且很麻烦。需要我们遍历所有人物的职业，然后才能确定射手类是我们想要的。

但是我们其实可以把这件事情交给程序来做。只要我们提出需求（物理伤害，射程远，持续输出），程序自动给我们返回一个射手类的对象。这样就很方便了。

这个帮我们创建类的类就是一个工厂类。这种编程模式，就是工厂模式。

工厂模式就是不直接暴露对象的创建细节，而是通过一个共同的类来创建对象的模式，总共需要四个步骤：

1. 创建基类
2. 创建子类
3. 创建工厂类
4. 使用工厂模式

事实上，工厂类中待我们选择的类并不要求非要继承自同一个父类。而且其实 Python 中所有类都继承自 project 类。所以前两步可以合并为一步，创建类。

我们可以这样创建一个工厂模式的类：

```python
# 创建基类
class Person(object):
    def __init__(self, name):
        self.name = name
    def get_name(self):
        return self.name

# 创建子类
class Male(Person):
    def __str__(self):
        return 'Hello Mr.' + self.get_name()
class Female(Person):
    def __str__(self):
        return 'Hello Miss' + self.get_name()
    
# 创建工厂类
class Factory(object):
    def get_person(self, name, gender='M'):
        if gender == 'M':
            return Male(name)
        if gender == 'F':
            return Female(name)
        
# 使用工厂模式
factory = Factory()
person = fcatory.get_person('Bob', 'M')
print(person)
```