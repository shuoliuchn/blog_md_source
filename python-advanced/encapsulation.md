## 封装

“封装”就是将抽象得到的数据和行为相结合，形成一个有机整体

- 元组，列表，字典等等：数据的封装，通过引用去使用数据
- 函数：算法的封装
  - 如果没有函数，功能要靠每一行代码去直接执行
  - 没有函数的话，耦合度会很高，复用性很差，开发效率也非常低

封装的目的是简化编程和增加安全性

- 使用者不必关系具体的实现细节
- 通过接口（万能的点 `.`）
- 还可以给予特定的访问权限来使用类的成员
- 明确区分内外
  - `类的实现者` 可以修改内部封装的东西而不影响外部调用者
  - `外部调用者` 只需要直到自己可以使用该类对象的哪些功能

私有属性和私有方法

- 标识符以双下划线 `__` 开头的是私有成员，在外部不可以直接用这种属性或方法名调用，子类中也不能访问到这个数据
- 可以提供外界访问的接口
  - 将不需要对外提供的内容都隐藏起来
  - 把属性都隐藏，提供公共方法对其访问
- 双下划线开头的属性在继承给子类时，子类是无法覆盖的

私有方法示例：

```python
class Person:
    def __init__(self, name, age):
        self.__name = name
        self.__age = age
    def get_age(self):
        return self.__age
    def set_age(self, age):
        self.__age = age
xiaoming = Person('小明', 17)
# print(xiaoming.__age)    # 程序会报错，找不到__age属性
print(xiaoming.get_age())
xiaoming.set_age(20)
print(xiaoming.get_age())
```

输出的结果为：

```python
17
20
```

私有属性和私有方法并不是绝对不可以在外部访问和修改。我们可以通过在私有属性或方法名前加上 `_类名`，就可以调用私有属性和方法了：

```python
class Person:
    def __init__(self, name, age):
        self.__name = name
        self.__age = age
    def get_age(self):
        return self.__age
    def set_age(self, age):
        self.__age = age
xiaoming = Person('小明', 17)
print(xiaoming._Person__age)
xiaoming._Person__age = 20
print(xiaoming._Person__age)
```

破解私有属性和私有方法：

- 在名称前加上 `_类名`
- 其实双下划线仅仅是一种变形操作：
  - 类中所有双下划线开头的名称如 `__x` 都会自动变形成：`_类名__x` 的形式

封装总结

- 对外隐藏类内的具体实现，只提供接口，供外界使用
- 可以将属性和方法设置成私有的，`__`，设置成私有后，类内部可以访问，类外部无法访问