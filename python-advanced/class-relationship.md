## 类之间的依赖关系和组合关系

[TOC]

依赖关系指的是在一个类的方法中调用了另一个类的对象

组合关系则是指一个类的属性是其他类的对象（一对一和一对多）

### 依赖（关联）关系

类之间可以有三种关系：

1. 依赖（关联）关系
2. 组合（聚合）关系
3. 继承（实现）关系

依赖（关联）关系指的是类对象执行某个动作的时候，需要其他类的对象来帮助完成这个操作的情况，其特点为：

- 将一个类的对象或者类名传到另一个类的方法中使用
- 此时的关系是最轻的，随时可以更换其他对象

关联关系的示例如下：

```python
class Person:
    def play(self, tools):
        tools.run()
        print('我要打游戏了')
class Computer:
    def run(self):
        print('电脑已经打开，DNF已经登陆')
class Phone:
    def run(self):
        print('王者荣耀已经登陆')
xiaoming = Person()
xmPhone = Phone()
hwComp = Computer()
xiaoming.play(xmPhone)
xiaoming.play(hwComp)
```

输出的结果为：

```python
王者荣耀已经登陆
我要打游戏了
电脑已经打开，DNF已经登陆
我要打游戏了
```

在这里补充两个删除对象的方法：

```python
delattr(对象名, '属性名')    # 动态删除时比较常用
del 对象名.属性名
```

`del` 和 `delattr` 功能有限，只能删除实例属性，不能删除类属性：

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
del xiaoli.age
delattr(xiaohong, 'age')
print(xiaoli.__dict__)
print(xiaohong.__dict__)
```

### 组合（聚合）关系

组合（聚合）关系是对象里包含对象的关系：

- 将一个类的对象封装到另一个类的对象的属性中，就叫组合
  - 一对一关系
  - 一对多关系

一对一关系示例：

```python
class BigB:
    def __init__(self, name, girl_friend=None):
        self.name = name
        self.girl_friend = girl_friend
    def eat(self):
        if self.girl_friend:
            print(f'{self.name}带着他的女朋友{self.girl_friend.name}去吃饭')
        else:
            print('单身狗，吃狗粮！')
    def movie(self):
        if self.girl_friend:
            print(f'{self.name}带着他的女朋友{self.girl_friend.name}去看电影')
        else:
            print('单身狗不陪看电影！')
            
class Girl:
    def __init__(self, name):
        self.name = name
    
bao = BigB('宝元')
friend = Girl('唐艺昕')
bao.eat()
bao.movie()
bao.girl_friend = friend
bao.eat()
bao.movie()
```

输出的结果为：

```python
单身狗，吃狗粮！
单身狗不陪看电影！
宝元带着他的女朋友唐艺昕去吃饭
宝元带着他的女朋友唐艺昕去看电影
```

### 一对多的组合关系

同一对一的组合关系类似，我们可以将多个类的对象封装为另一个类的属性，具体示例为：

```python
# 有一个男孩类和一个女孩类，男孩类中包含多个女孩类的对象
class Boy:
    def __init__(self, name):
        self.name = name
        self.girl_f = []
    def ba_mei(self, girl):
        self.girl_f.append(girl)
    def happy(self):
        for i in self.girl_f:
            i.play()
class Girl:
    def __init__(self, name):
        self.name = name
    def play(self):
        print(f'{self.name}陪你一起玩~o(=•ェ•=)m')
        
xiaoqiang = Boy('小强')

xiaohong = Girl('小红')
xiaoli = Girl('小丽')
xiaofei = Girl('小菲')

xiaoqiang.ba_mei(xiaohong)
xiaoqiang.ba_mei(xiaoli)
xiaoqiang.ba_mei(xiaofei)

xiaoqiang.happy()
```

输出的结果为：

```python
小红陪你一起玩~o(=•ェ•=)m
小丽陪你一起玩~o(=•ェ•=)m
小菲陪你一起玩~o(=•ェ•=)m
```

组合关系与依赖关系的区别在于，组合关系是将类对象封装为新类的属性来使用，而依赖关系则是将类对象作为参数传给新类的方法使用。

一对多练习：

```python
创建教师类和学生类
• 教师类有姓名和学生列表两个属性
• 教师类有添加学生的方法（添加的学生是具体对象）
• 教师类有显示对应学生姓名和学号的方法
• 学生类有学号/姓名/教师姓名三个属性
• 创建多个学生，并添加到某位教师的学生列表中
• 打印该教师的学生
```

参考代码如下：

```python
class Teacher:
    def __init__(self, name):
        self.name = name
        self.student_list = []
    def add_student(self, *student):    # student前加了*，为了能一次性接收多个实例对象，方便后续使用
        self.student_list.extend(student)
        for j in student:
            j.teacher = self.name
    def show_student(self):
        for i in self.student_list:
            print(f'姓名：{i.name}，学号：{i.student_id}')


class Student:
    def __init__(self, name, student_id):
        self.name = name
        self.student_id = student_id
        self.teacher = None

alex = Teacher('Alex')
bigB = Teacher('宝元')

xiaoming = Student('小明', 123)
xiaoqiang = Student('小强', 234)
xiaohong = Student('小红', 345)
xiaoli = Student('小丽', 456)
xiaohu = Student('小虎', 135)

alex.add_student(xiaoming ,xiaoli)
bigB.add_student(xiaoqiang, xiaohong, xiaohu)

alex.show_student()
bigB.show_student()

print(xiaoming.teacher)
print(xiaoqiang.teacher)
```

需要注意，使用组合关系时，要尽量避免两个或多个类之间混合使用彼此的类对象作为自己的类属性，这样会陷入循环调用的麻烦中，容易引发混乱。