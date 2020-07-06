## 函数的名称空间

函数的名称空间一共有三种：

1. 内置空间，用来存放 Python 自带的一些函数，Python 程序运行时会首先加载
2. 全局空间，当前 py 文件顶格编写的代码开辟的空间
3. 局部空间，函数开辟的空间

程序的加载顺序是：内置空间 > 全局空间 > 局部空间

程序的取值顺序是：局部空间 > 全局空间 > 内置空间

程序取值顺序示例：

```python
a = 10
def func():
    a = 5
    print(a)
func()

输出的结果为： 5
```

变量取值时会优先查看局部空间，找到变量 a，值为 5，打印了出来。

函数的作用域有两个：

1. 全局作用域：内置空间 + 全局空间，使用 `globals()` 方法查看全局作用域
2. 局部作用域：局部空间，使用 `locals()` 方法查看当前作用域（全局和局部作用域都可以查看，建议用此方法查看局部作用域）

```python
a = 10
def func():
    b = 5
    print(globals())
    print(locals())

func()
print(globals())
print(locals())
```