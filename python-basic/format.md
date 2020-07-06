## 字符串的格式化

[TOC]

有这样一个字符串：

```python
msg = """
------info------
name:meet
age:18
sex:男
hobby:女
-------end------
"""
```

如果我们想让用户输入名字，年龄，性别和爱好，然后程序按照上面的格式给打印出来。从目前我们所学的知识，我们可以用这样的代码来实现：

```python
a = "------info------"
b = "name: "
c = "age: "
d = "sex: "
e = "hobby: "
f = "-------end------"
name = input(b)
age = input(c)
sex = input(d)
hobby = input(e)
print(a)
print(b + name)
print(c + age)
print(d + sex)
print(e + hobby)
print(f)
```

代码运行后就是这样的：

```python
name: alex
age: 18
sex: 男
hobby: 女
------info------
name:alex
age:18
sex:男
hobby:女
-------end------
```

### % 格式化

不过虽然我们实现了需求，但是太过繁琐。这里就可以用到格式化的方法。格式化，就是在字符串中需要自定义的位置放入占位符，然后通过给占位符提供数据，从而构建新的字符串。提供的数据需要和占位符一一对应，否则将会报错。

Python中常用的占位符有：

- `%s` 字符串：`%s` 可以填充字符串也可以填充数字（其实也可以是其他数据类型）
- `%d` | `%i` 整型：必须填充数字
- `%%` 转义：变成普通的 `%`

有了格式化的方法，上面的例子我们就可以简化成这个样子：

```python
name = input("name: ")
age = input("age: ")
sex = input("sex: ")
hobby = input("hobby: ")
a = "哈哈啊"
msg = """
------info------
name: %s
age: %s
sex: %s
hobby: %s
-------end------
"""
print(msg % (name, int(age), sex, hobby))
```

### f-strings 格式化

在 Python 3.6 及以后的版本中引入了一个新的 `f-strings` 方法格式字符串，把上面的格式化方法进一步简化，具体做法为：

```python
msg = f"""
------info------
name: {input("name: ")}
age: {input("age: ")}
sex: {input("sex: ")}
hobby: {input("hobby: ")}
-------end------
"""
print(msg)
```

在字符串的引号前，用小写字母 `f` 声明字符串为 `f-strings` 格式的字符串。用大括号将需要格式化的位置标记出来，在大括号里面填入变量或者数据，构建成新的字符串。

可以通过两个大括号来进行转义，表示普通的大括号：

```jinja2
{{ }}
```

f-strings 的更详细用法，可以参见  [f-strings 详解](f-strings.md)。

### 字符串的 `.format()` 方法

`.format()` 方法是另外一种字符串格式化的方法：

```python
s = 'alex{}wu{}si{}r'
s1 = s.format('你好', '我好', '大家好')
print(s1)

输出的结果为：alex你好wu我好si大家好r
```

`.format()` 方法除了向上面这种按照位置格式化之外，还可以按照索引格式化：

```python
s = 'alex{0}wu{2}si{1}r'
s1 = s.format('你好', '我好', '大家好')
print(s1)

输出的结果为：alex你好wu大家好si我好r
```

`.format()` 方法还可以按照关键字进行格式化：

```python
s = 'alex{a}wu{c}si{b}r'
s1 = s.format(b = '你好', a = '我好', c = '大家好')
print(s1)

输出的结果为：alex我好wu大家好si你好r
```