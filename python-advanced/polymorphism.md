## 多态

[TOC]

### 多态体现一，变量可以指向任意数据类型

多态的第一种体现是，不关注对象的数据类型，同一个变量完全可以在不同的时间引用不同的对象

Python 本身就是一种多态语言，不关心对象的类型

- 对于弱类型的语言来说，变量并没有声明类型，因此同一个变量完全可以在不同的时间引用不同的对象
- 毫无疑问，在 Python 中，对象是一块内存，内存中除了包含属性和方法之外，还包含了对象类型。我们通过引用来访问对象，比如 `a = A()`。首先，Python 创建一个对象 A，然后声明一个变量 a，再将变量 a 与对象 A 联系起来。变量 a 是没有类型的，它的类型取决于其关联的对象。

```python
a = 10
a = 'alex'    # Python中的变量可以随时被赋值任意数据类型
```

我们曾经谈到过，Python 中的每一个类实际上就是一个数据类型，因此，变量也可以指向不同类的对象：

```python
class Bird:
    def move(self, field):
        print('鸟儿在%s上自由地飞翔~' % field)
class Dog:
    def move(self, field):
        print('狗狗在%s里飞快地奔跑~' % field)
x = Bird()
x.move('天空')
x = Dog()
x.move('草地')
```

输出的结果为：

```python
鸟儿在天空上自由地飞翔~
狗狗在草地里飞快地奔跑~
```

同一个变量x在执行同一个 `move()` 方法时，由于 x 指向的对象不同，因此呈现出不同的行为特征，这就是多态。

### 多态体现二，一类事物有多种形态（Polymorphic）

一个抽象类有多个子类，但方法实现不同

- 例如：动物类有多个子类，每个子类都重新实现了父类的某个方法，但方法的实现不同（比如动物类都有休息的方法，但不同的动物休息的方式不尽相同）
- 此时需要有继承，需要方法重写

```python
class Girl(object):
    def __init__(self, name):
        self.name = name

    def game(self):
        print('%s蹦蹦跳跳地玩耍...' % self.name)


class Nurse(Girl):
    def game(self):
        print('%s脱裤子打针' % self.name)


class Boy(object):
    def __init__(self, name):
        self.name = name

    def game(self, girl):
        print('%s和%s快乐地玩耍' % (self.name, girl.name))
        girl.game()


xiaoming = Boy('小明')
xiaohong = Girl('小红')
xiaoli = Nurse('小丽')

xiaoming.game(xiaohong)
xiaoming.game(xiaoli)
```

输出的结果为：

```python
小明和小红快乐地玩耍
小红蹦蹦跳跳地玩耍...
小明和小丽快乐地玩耍
小丽脱裤子打针
```

Nurse 有 Girl 类的方法和属性，是它的子类。方法名虽然相同，但实现的方式略有差异。具有相同父类，继承相同方法，在子类中对父类的方法进行了修改，就是多态的第二种体现。

### 多态性

多态性（polymorphism）指的是，在多态基础上，定义统一的共同接口（在类的外面定义单独的函数），从而实现使用一个函数调用不同类的同名方法。

不同类的对象作为函数的参数时，因为方法不同，得到的结果也会有差异。

多态性的具体实现方法如下：

```python
class Person:
    def relax(self):
        print('人需要休息')
class Alex:
    def relax(self):
        print('睡觉')
class BigB:
    def relax(self):
        print('买沙发')
        
# 定义一个函数作为访问多个类的relax方法的统一接口
def relax_func(person):
    person.relax()
    
a = Alex()
b = BigB()
p = Person()

relax_func(a)
relax_func(b)
relax_func(p)
```

输出的结果为：

```python
睡觉
买沙发
人需要休息
```

### 鸭子类型

鸭子类型是 Python 中比较推崇的多态的另一种体现形式。

鸭子类型不关心类型，不需要继承，只关注方法的实现，这种情况被称为鸭子类型。

鸭子类型名字的来源于一句话：“当看到一只鸟走起来像鸭子、游起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称作‘鸭子’”。

套用过来就是，当两个类，有同名方法，不管这两个类是干什么用的，也不管它们之间有没有联系（依赖、组合或继承），都可以用同一个接口来调用。

在鸭子类型中，关注的不是对象的类型本身，而是它是如何使用的。

比如，人、鸭子、鱼、猪和船都会游泳，但是他们是不同的类，他们之间没有任何联系。而我们却可以在外面定义一个统一的接口来调用他们之间游泳的方法：

```python
class Person:
    def swim(self):
        print('人可以游泳')
class Duck:
    def swim(self):
        print('鸭子会游泳')
class Fish:
    def swim(self):
        print('鱼会游泳')
class Pig:
    def swim(self):
        print('猪会游泳')
class Ship:
    def swim(self):
        print('船会游泳')
   
# 建立鸭子类型
def swimming(obj):
    obj.swim()
    
xiaoming = Person()
xiaohuang = Duck()
paopao = Fish()
peiqi = Pig()
titanic = Ship()

swimming(xiaoming)
swimming(xiaohuang)
swimming(paopao)
swimming(peiqi)
swimming(titanic)
```

输出的结果为：

```python
人可以游泳
鸭子会游泳
鱼会游泳
猪会游泳
船会游泳
```

### 多态总结

Python 是一门天然支持多态的编程语言。

多态增加了程序的灵活性（通用性），以不变应万变，不论对象千变万化，使用者都是使用同一种形式（外部定义的一个函数）去调用。

增加程序的可扩展性，通过继承某个类创建的一个新类，接口使用者无需更改自己的代码，还是用原方法调用。

多态和多态性对比：

多态强调的是，一类事物由不同的形态。

多态性强调的是，同一操作，作用对象不同，表现出不同的实现方式（只关心行为结果）