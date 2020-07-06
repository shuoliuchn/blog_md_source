## 字符串方法详解

[TOC]

字符串方法有很多，这里只讨论一些常用的，使用万能的点操作字符串的方法。

因为字符串是不可变数据类型，所以字符串方法不会在字符串本身进行修改，而是将修改后的新字符串作为返回值返回。故而若要使用字符串方法，需要使用变量来接收才行。

### `.upper()` 方法

`.upper()` 方法可以将字符串中的所有小写字母转换为大写字母：

```python
name = "alex"
name1 = name.upper()  # 全部大写
print(name)
print(name1)
```

输出的结果为：

```python
alex
ALEX
```

### `.lower()` 方法

`.lower() `方法与 `.upper()` 方法刚好相反，是将字符串中所有的大写字母转换为小写字母：

```python
name = "ALEX"
name1 = name.lower()  # 全部小写
print(name)
print(name1)
```

输出的结果为：

```python
ALEX
alex
```

`.upper()` 和 `.lower ` 方法的一个很常见的应用场景是一些不需要区分大小写的情况，比如输入验证码或用户名时：

```python
yzm = "0G8k"
my_yzm = input("请输入验证码:[0G8k]")
if yzm.lower() == my_yzm.lower():
    print("ok")
else:
    print("滚")
```

### `.startswith()` 方法

`.startswith()` 方法用来判断字符串是否以指定的参数开头，返回的是布尔值：

```python
name = 'alex'
print(name.startswith('a'))

返回的结果是： True
```

`.startswith()` 方法还支持字符串的 `切片`，判断切片后的字符串是否是以相应的参数开头：

```python
name = 'alex'
print(name.startswith('l', 1, 3))

返回的结果是： True
```

### `.endswith()` 方法

`.endswith()` 的用法跟 `startswith()` 十分相似。不同的是，它是用来判断字符串是否以指定的字符串结尾，返回的同样是布尔值。`.endswith()` 方法同样支持切片操作。

```python
name = 'alex'
print(name.endswith('x'))

返回的结果是： True
```

### `.count()` 方法

`.count()` 方法用来统计输入的参数在字符串中出现的次数，例如：

```python
name = "meet_alex"
print(name.count("e"))

返回的结果是： 3
```

### `.strip()` 方法

补充知识：`\n` 为 `换行符`，也就是键盘上的 `回车` 键；`\t` 为 `制表符`，也就是键盘上的 `Tab` 键：

```python
print("你\n好")    # 换行就是键盘上的回车键
print("你\t好")    # 制表符就是键盘上的 Tab
```

输出的结果为：

```python
你
好
你	好
```

`.strip()` 方法用来去除字符串两端的空格、换行符和制表符：

```python
name = '   \nalex   \t   '
print(name.strip())
```

输出的结果为：

```python
alex
```

`.strip()` 方法也可以通过设定参数来指定去除头尾两端的内容：

```python
name = 'aaaaa aelx  \naa\ta\naaaaa'
print(name.strip('a'))
```

输出的结果为：

```python
 aelx
aa      a

```

需要注意的是，当指定参数后，将不会清除空格、换行符和制表符。

`.strip()` 方法的应用场景是当输入账号密码时，忽视首位无意间输入的或者复制粘贴过来的空格：

```python
user = input("账号：").strip()
pwd = input("密码：").strip()
if user == "alex" and pwd == "alex123":
    print("ok")
else:
    print("滚")
```

另外，`int()` 内部封装了 `.strip()` 方法。故而当使用 `int()` 函数将字符串转换为整型时，不需要额外的 `.strip()` 操作：

```python
num = ' \t 23 \n '
print(int(num))
```

### `.split()` 方法

`.split()` 方法用来分割字符串。默认按照空格、换行符和制表符来进行分割。分割后，空格、换行符和制表符将不存在。`.split()` 方法输出的是列表：

```python
a = "alex alex123"
lst = a.split()
print(lst)
```

输出的结果为：

```python
['alex', 'alex123']
```

空格、换行符和制表符都有同样的结果：

```python
name = "alex\nmeet"
print(name.split())
name = "alex\tmeet"
print(name.split())
name = "alex meet"
print(name.split())
```

`.split()` 方法可以指定参数，通过特定的内容进行分割：

```python
a = "alex:alex123"
lst = a.split(":")
print(lst)
```

输出的结果同样是：

```python
['alex', 'alex123']
```

### `.replace()` 方法

`.replace()` 方法可以替换字符串中的旧内容为新内容。`.replace()` 有三个参数：参数 1 为旧值，参数 2 为新值，参数 3 为替换次数（默认全部替换）。例如：

```python
name = 'alexmeet'
name = name.replace('e', 's', 2)
print(name)

输出的结果是：alsxmset
```

### `is` 系列方法（判断系列）

`is` 系列方法有很多，主要用到的是如下几个：

- `name.isalnum()`：用来判断是不是由字母、中文或数字组成，返回的是布尔值
- `name.isalpha()`：用来判断是否由字母或中文组成，返回布尔值
- `name.isdigit()`：用来判断是否由阿拉伯数字组成，返回布尔值。有一个 bug 是，像 ⑤ 这样的字符也会被认作阿拉伯数字
- `name.isdecimal()`：用来判断是不是由十进制组成，返回的是布尔值
- `name.islower()`：用来判断字符串中的字母（包括英文字母和希腊字母等）是不是全部小写，中文和数字不考虑（如果全部是中文和数字或是空字符串，返回 False）
- `name.isupper()`：用来判断字符串中的字母（包括英文字母和希腊字母等）是不是全部大写，中文和数字不考虑（如果全部是中文和数字或是空字符串，返回 False）