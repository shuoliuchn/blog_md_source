## 函数名的第一类对象及使用

函数名的第一类对象只是一种称呼，是相对于第二类对象而言的。我们目前用到的函数基本都是第一类对象。

函数名的第一类对象主要有四个方面的应用：

1. 函数名可以当作值赋值给一个变量
2. 函数名可以当做另一个函数的参数来使用
3. 函数名可以当做另一个函数的返回值
4. 函数名可以当作元素放在容器中

示例一：

```python
def func():
    print(1)

a = func
print(func)  # 函数的内存地址
print(a)
a()
```

输出的结果为：

```python
<function func at 0x00000197E2D41EA0>
<function func at 0x00000197E2D41EA0>
1
```

`func`和`a`同样都是指向函数的内存地址，当我们调用`a`时，得到了同调用`func`相同的结果。

示例二：

```python
def func():
    print(1)
def foo(a):
    print(a)
foo(func)
```

输出的结果为：

```python
<function func at 0x0000021FD82C1EA0>
```

示例三：

```python
def func():
    return 1
def foo(a):
    return a
cc = foo(func)
print(cc)
```

输出的结果是：

```python
<function func at 0x000002261A231EA0>
```

示例四：

有这样一个需求：用户选择需要相应的序号进行选择接下来的操作，比如选择 1 会调用注册的函数，选择 2 会调用登陆的函数……从前我们会通过流程控制语句的方式实现：

```python
def login():
    print("登录")

def register():
    print("注册")

def shopping():
    print("逛")

def add_shopping_car():
    print("加")

def buy_goods():
    print("买")

msg ="""
1.注册
2.登录
3.逛
4.加
5.买
请输入您要选择的序号:
"""
while True:
    choose = input(msg)
    if choose.isdecimal():
        if choose == "1":
            register()
        elif choose == "2":
            login()
        elif choose == "3":
            shopping()
        elif choose == "4":
            add_shopping_car()
        elif choose == "5":
            buy_goods()
        else:
            print("滚")
```

通过将函数装到字典里，我们可以大量减少重复的代码：

```python
def login():
    print("登录")

def register():
    print("注册")

def shopping():
    print("逛")

def add_shopping_car():
    print("加")

def buy_goods():
    print("买")

msg ="""
1.注册
2.登录
3.逛
4.加
5.买
请输入您要选择的序号:
"""
func_dic = {"1":register,
            "2":login,
            "3":shopping,
            "4":add_shopping_car,
            "5":buy_goods}
while True:
    choose = input(msg) # "5"
    if choose in func_dic:
        func_dic[choose]()
    else:
        print("滚")
```

这种使用字典调用函数的方式是一种很重要的编程思想，以后将经常用到。