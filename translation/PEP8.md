# PEP 8 -- Python代码格式规则

| PEP:           | 8                                                            |
| :------------- | ------------------------------------------------------------ |
| 标题：         | Python代码格式规则（Style Guide for Python Code）            |
| 作者：         | Guido van Rossum <guido at python.org>, Barry Warsaw <barry at python.org>, Nick Coghlan <ncoghlan at gmail.com> |
| 译者：         | 刘硕                                                         |
| 状态：         | 有效                                                         |
| 类型：         | Process                                                      |
| 创建日期：     | 2001年7月5日                                                 |
| 发布历史：     | 2001年7月5日，2013年8月1日                                   |
| 翻译完成日期： | 2020年1月13日                                                |

------

[TOC]

# 概述

本文介绍的是 Python 主流发行版标准库的代码书写格式。对于嵌套在 Python 中执行的 C 语言代码的书写格式，请参照另外一个 PEP 代码格式规则[^1]。

本文档和 [PEP 257](https://www.python.org/dev/peps/pep-0257) （文档字符串书写规则）都是依据 Guido 写过的 Python 格式规则修订而来的[^2]。

本格式规则不是一成不变的，曾经的书写传统会随着语言的变化而发生改变。

许多项目都有它们自己的编码格式规则。如果本文提出的格式指导与项目格式要求产生了冲突，要优先使用项目规定的格式。

# 教条主义是脑袋瓜中的小妖精

Guido 认为，代码被阅读的频率要比它被书写的频率高。这篇格式规则就是要试着提高代码的可读性，让不同人写的代码都能在 Python 领域中广泛流传。就像 Python 之禅 [PEP 20](https://www.python.org/dev/peps/pep-0020) 中提到的那样：“可读性很重要”。

格式规则就是为了统一格式。按照本格式规则写代码非常重要，按照项目规定的格式更重要。最重要的是，始终遵守同一种格式要求。切忌混用格式要求。

然而，要知道何时需要打破规则——有时格式规则建议的规则不适用。在犹豫不决时，大胆使用你觉得最合适的格式。多尝试几种写法，选取看起来最顺眼的那个。你也可以向别人请教哟！

特别地：不要为了遵守这个 PEP 规则而打破向后兼容性！

无视某条规则的其他原因：

1. 使用规则反而降低代码的可读性，就连那些常年阅读遵循此 PEP 规则程序的程序员读起来也很吃力。
2. 为了跟周围的代码一致，有时不得不打破这个规则（或许是因为历史原因）——不过这也是一个清理他人留下的烂摊子的一个机会（现实版的 XP 风格）。
3. 涉及的那段代码早在引入格式规则之前就已经写好了，而且没有其他理由修改它们。
4. 那段代码需要和比较老版本的 Python 代码保持一致，而它又恰好不支持我们建议的格式规则。

# 代码布局

## 缩进

每一级缩进用**四个空格**表示。

将连续的一行写成多行，新起的一行要么和 Python 内置的包裹元素圆括号、方括号或大括号对齐，要么启用悬挂缩进[^7]。使用悬挂缩进时，下面几种方法可供参考。不言而喻的是，第一行和后续的缩进行要能明显区分出来，不要把分行写的内容和缩进内容搞混（参见推荐写法的第二个例子）。

推荐写法：

```python
# 和开放的分隔符（这里是小括号）对齐
foo = long_function_name(var_one, var_two,
                         var_three, var_four)

# 增加四个空格（多一级缩进），从而区分参数和后续的函数代码。
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)

# 悬挂缩进应该多出一级。
foo = long_function_name(
    var_one, var_two,
    var_three, var_four)
```

不合适的写法：

```python
# 第一行的两个参数没有另起一行，妨碍阅读。
foo = long_function_name(var_one, var_two,
    var_three, var_four)

# 不能直观区分分行写的代码和后续需要缩进的代码。
def long_function_name(
    var_one, var_two, var_three,
    var_four):
    print(var_one)
```

对于分行写的情况，可以不遵守4空格规则。

上面的例子也可以写成这样：

```python
# 悬挂缩进可以不严格使用4个空格。
foo = long_function_name(
  var_one, var_two,
  var_three, var_four)
```

如果 `if` 语句的条件很长，需要分多行来写，你应该注意的是，关键字占两个字符（比如 `if`），加上一个空格，再加上一个左括号，刚好凑成4个字符。若此时还使用上面的规则，在与括号后面对其，新一行天然缩进4个字符，这会跟后续的 `if` 语句重叠。对于这种层叠起来的 `if` 语句，本 PEP 规则不会要求如果（或者是否）进一步作出可视化的区分。这种情可接受的写法，包括但不限于：

```python
# 不额外增加缩进
if (this_is_one_thing and
    that_is_another_thing):
    do_something()

# 增加一条注释，为程序员或多或少提供一些区分
# supporting syntax highlighting.
if (this_is_one_thing and
    that_is_another_thing):
    # Since both conditions are true, we can frobnicate.
    do_something()

# 给条件句增加额外的缩进
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```

（也请参见下面关于[改在二元运算符的前面还是后面换行](#该在二元运算符的前面还是后面换行)的讨论）

多行的闭合大括号、方括号、圆括号结构的最后一行**回括号**要么与括号中内容的最后一行对齐，例如：

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
    )
```

要么与多行结构第一行的第一个字符对齐，例如：

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
)
```

## 制表符还是空格？

推荐使用空格缩进。

制表符（`Tab` 键）仅在为了与已经使用制表符缩进的代码保持一致的情况下使用。

Python 3 不允许混用制表符和空格缩进。

Python 2 混用制表符和空格缩进的代码应该转换成统一使用空格缩进。

使用 Python 2 命令行解释器的 `-t` 选项时，如果代码非法混用了制表符和空格，会发出警告。如果使用 `-tt` 选项，这些警告会报错。极力推荐运行程序时使用这些命令！

## 单行最大长度

把每一行的长度都限制在 79 个字符以内。

对于没有很多结构要求的灵活的大块文本（文档字符串或注释），单行长度要限制在 72 个字符以内。

限制编辑的窗口宽度使得我们可以并排打开多个文件。而且，使用代码版本控制工具时，也可以在相邻的两列比较不同版本的差异。

绝大多数代码编辑工具的默认宽度都会破坏代码的可视结构，让代码更难理解。即便有些编辑器在自动换行时会提供特殊的图形标识，也于事无补。选择79个字符的限制就是为了避免那些窗口宽度被设置为80的编辑器破坏我们的代码结构。还有一些基于网页的编辑工具可能根本就不会动态换行。

有些团队，极度想要更长的代码。出于特别的维护代码的目的，或者团队一致同意，也可以把单行代码长度限制提高到99个字符。注释和文档字符串仍然建议限制在每行72个字符以下。

Python 标准库保守地要求每一行限制在79个字符以下（文档字符串/注释为72个字符）。

要想把一行长代码分割成多行，推荐的方式是将圆括号、方括号和圆括号中的内容换行处理。括号中的长代码有时会因换行而损坏。这时，就应该使用反斜杠来分割代码中的其他部分。

反斜杠有时很有用。比如，很长的，多个 `with` 语句不能在括号中换行，所以更倾向于使用反斜杠换行：

```python
with open('/path/to/some/file/you/want/to/read') as file_1, \
     open('/path/to/some/file/being/written', 'w') as file_2:
    file_2.write(file_1.read())
```

（参见前面关于[多行 `if` 语句](#缩进)的讨论，更加深入思考这种多 `with` 语句的缩进写法）

这种情况的另一个例子是 `assert` 语句。

确保每一行的缩进都是恰当的。

## 该在二元运算符的前面还是后面换行

数十年来，程序员们约定俗成的规则是在二元运算符后面换行。但是这种换行方式会在两个方面破坏代码的可读性：运算符会在屏幕的不同列四散分布，每个运算符都在远离运算域的前一行结尾。在这种情况下，我们的眼睛需要额外的工作来分辨到底是要做加法还是做减法：

```python
# 不合适的写法：运算符处于原理运算域的位置
income = (gross_wages +
          taxable_interest +
          (dividends - qualified_dividends) -
          ira_deduction -
          student_loan_interest)
```

为了解决这种可读性的问题，数学家和出版商都遵循相反的规则。Donald Knuth 在他的系列著作 *计算机与排版* 中这样解释这个规则：“虽然在一整段文字中的公式会在二元运算符的后面换行，但当单独展示公式时，要在二元运算符前面换行”[^3]。

若遵守传统的数学排版规则，写出的代码就会更具可读性：

```python
# 推荐写法：易于将运算符与运算域相匹配
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends)
          - ira_deduction
          - student_loan_interest)
```

在 Python 代码中，在二元运算符前后换行都是可以的，但是要保持一致性，不能混着用。对于新写的代码，还是建议使用 Knuth 的格式，在二元运算符之前换行。

## 空行

最外层的函数和类在定义时，要用两个空行分隔开。

在定义类的方法时，要用一个空行分隔。

可以用额外的空行（越少越好）区分一组有关联的函数。可是适当删除一组有关联的单行代码（比如一组虚拟工具[^8]）之间的空行。

在函数中使用空行（越少越好）来区分不同的逻辑块。

Python 将 `control-L`（例如 `^L`）换页符认作空格。很多编辑器都将这些符号用作分页的标识。所以你可以使用这些符号将你文件中不同的部分的代码放到不同页中。需要注意的是，有些编辑器和基于网页的代码阅读器或许不能识别 `control-L` 和分页符，于是在那个位置或许不能正常分页显示。

## 源文件代码编写

Python 核心发行版的字符集一定是 UTF-8 （在 Python 2 中是 ASCII）。

使用 ASCII（Python 2）或 UTF-8（Python 3）编码的文件不必声明编码方式。

在标准库中，如果出于测试目的，或者出现在注释或文档字符串中需要提到一个不能用 ASCII 字符表示的作者名字，可以不使用默认编码。如果不是出入上述目的，请使用 `\x`、`\u`、`\U` 或者 `\N`  来转义那些非-ASCII 字符。

对于 Python 3.0 及之后的版本，标准库需要遵守如下规定（参见 [PEP 3131](https://www.python.org/dev/peps/pep-3131)）：Python 标准库中的**所有**标识符**必须只**使用 ASCII 符号，而且在所有可能的地方都**应该**使用英文单词（在很多情况，会用到非英文的缩写和技术术语）。此外，文档字符串和注释必须用 ASCII 编码。唯一的例外是（a）测试非-ASCII 特性，和（b）作者的名字。如果作者的名字不是基于拉丁字母（latin-1，ISO/IEC 8859-1 字符集）必须提供一个用此字符集音译的名字。

建议具有全球影响力的开源项目也遵守和 Python 标准库类似的规范。

## 导入

导入通常应该分成多行来写：

```python
合适:   import os
       import sys

不合适: import sys, os
```

然而这样写是可以的：

```python
from subprocess import Popen, PIPE
```

导入通常位于文件的顶端，模块注释和文档字符串之后，全局模块和常量之前。

导入应该按照下面的顺序分组排列：

1. 导入标准库；
2. 导入相关的第三方库；
3. 导入指定的本地应用/库。

每组导入之间应该用一个空行隔开。

建议使用绝对路径导入。因为这种导入方式可读性更高，而且当导入系统的路径配置不当（比如包中的路径以 `sys.path` 结尾）时，表现得更好（至少报错信息更明确）：

```python
import mypkg.sibling
from mypkg import sibling
from mypkg.sibling import example
```

不过，除了绝对路径导入，明确的相对路径导入也是可以接受的，尤其是处理的包文件结构布局很复杂，没有必要使用冗长的绝对路径导入的时候：

```python
from . import sibling
from .sibling import example
```

标准库的代码应该避免使用复杂的包文件结构布局，而且只能使用绝对路径导入。

永远**不要**使用不明确的相对路径导入。在 Python 3 标准库中，这样的导入方式已经被移除。

当从一个包含类的模块中导入一个类时，这样写通常是可以的：

```python
from myclass import MyClass
from foo.bar.yourclass import YourClass
```

如果这种导入方式与本地的名称冲突，就要使用明确的方式导入：

```python
import myclass
import foo.bar.yourclass
```

通过 `myclass.MyClass` 和 `foo.bar.yourclass.YourClass` 实用类。

应该避免使用通配符导入（`from <module> import *`），因为这会让我们搞不清楚命名空间中都有哪些名字，给读者和许多自动化工具造成困扰。使用通配符导入在一种情况下是合理的，那就是将内部接口重新声明为共用 API（例如，重写一个通过任意加速模块定义的 Python 程序接口，而且事先不知道的定义会被重写[^9]）。

即便使用这个方式重写名字，依然要遵守后面关于公共与私有接口的规则。

## 模块级双下名称

模块级的“双下”名称（例如，名称前后都有两条双下划线），比如`__all__`、`__author__`、`__version__`，等等，应该放在文档字符串之后，并且处于除了 `from __future__` 之外，任何导入之前。Python 强制要求，future-导入必须出现在任何其他除文档字符串之外的代码之前：

```python
"""这是示例模块
本模块应有尽有
"""

from __future__ import barry_as_FLUFL

__all__ = ['a', 'b', 'c']
__version__ = '0.1'
__author__ = 'Cardinal Biggles'

import os
import sys
```

# 字符串的引号

在 Python 中，单引号字符串和双引号字符串是完全一样的。本 PEP 规则不对此作任何建议。选择一个规则，并且遵守它。不过，当字符串中已经有了单引号或者双引号自负的时候，就要使用另一种引号来避免在字符串中使用反斜线转义。这会提高代码的可读性。

至于三对引号的字符串，使用双引号来和 [PEP 257](https://www.python.org/dev/peps/pep-0257) 的文档字符串传统保持一致。

# 表达式和语句中的空格

## 痛点[^10]

在下面的情况下，不要加入空格：

- 圆括号、方括号和大括号里面紧挨着的地方：

  ```python
  合适:   spam(ham[1], {eggs: 2})
  不合适: spam( ham[ 1 ], { eggs: 2 } )
  ```

- 末尾的逗号和回括号之间：

  ```python
  合适:   foo = (0,)
  不合适: bar = (0, )
  ```

- 逗号、分号和冒号的正前方：

  ```python
  合适:   if x == 4: print x, y; x, y = y, x
  不合适: if x == 4 : print x , y ; x , y = y , x
  ```

- 不过，在切片中，冒号的作用类似于一个二元运算符，它的两边应该有等量的空格（把它看作优先级最低的运算符）。在扩展切片中，两个冒号搭配的空格数量也应该是相等的。例外：切片参数被省略时，空格也被省略。

  合适：

  ```python
  ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3], ham[1:9:]
  ham[lower:upper], ham[lower:upper:], ham[lower::step]
  ham[lower+offset : upper+offset]
  ham[: upper_fn(x) : step_fn(x)], ham[:: step_fn(x)]
  ham[lower + offset : upper + offset]
  ```

  不合适：

  ```python
  ham[lower + offset:upper + offset]
  ham[1: 9], ham[1 :9], ham[1:9 :3]
  ham[lower : : upper]
  ham[ : upper]
  ```

- 调用函数时，传参括号的左括号正前方：

  ```python
  合适:   spam(1)
  不合适: spam (1)
  ```

- 索引或切片左括号的正前方：

  ```python
  合适:   dct['key'] = lst[index]
  不合适: dct ['key'] = lst [index]
  ```

- 为了让指定的运算符和其他运算符对齐而在它（或别的运算符）周围使用多于一个空格：

  合适：

  ```python
  x = 1
  y = 2
  long_variable = 3
  ```

  不合适：

  ```python
  x             = 1
  y             = 2
  long_variable = 3
  ```

## 其他建议

- 时刻避免在结尾加空格。因为结尾的空格通常是看不见的，会造成令人困惑的麻烦：例如，一个反斜杠后面接一个空格，那么下一行就不被视为本行的延续。一些编辑器不会对此做出任何修改，而且很多项目（像 CPython 本身）在预加载过程中就会直接拒绝。

- 千万在二元运算符两端放一个空格：赋值运算（`=`）、增强赋值运算[^11]（`+=`、`-=`，等等）、比较运算（`==`、`<`、`>`、`!=`、`<>`、`<=`、`>=`、`in`、`not in`、`is`、`is not`）、布尔运算（`and`、`or`、`not`）。

- 如果运算符的优先级不同，可以考虑只在最低优先级运算符两端使用空格。具体在哪里加，由你自己来决定。然而，永远不要使用超过一个空格，而且一定保证二元运算符两端的空格数相等。

  合适：

  ```python
  i = i + 1
  submitted += 1
  x = x*2 - 1
  hypot2 = x*x + y*y
  c = (a+b) * (a-b)
  ```

  不合适

  ```python
  i=i+1
  submitted +=1
  x = x * 2 - 1
  hypot2 = x * x + y * y
  c = (a + b) * (a - b)
  ```

- 函数注释应该使用常规的冒号使用规则。如果有 `->` 箭头的话，要在它两边加空格。（参见下面的 [函数注释](#函数注释) 了解有关函数注释的更多细节）

  合适：

  ```python
  def munge(input: AnyStr): ...
  def munge() -> PosInt: ...
  ```

  不合适：

  ```python
  def munge(input:AnyStr): ...
  def munge()->PosInt: ...
  ```

- 在指明函数关键字参数或指明无注释函数参数默认值的等号 `=` 周围不要使用空格：

  合适：

  ```python
  def complex(real, imag=0.0):
      return magic(r=real, i=imag)
  ```

  不合适：

  ```python
  def complex(real, imag = 0.0):
      return magic(r = real, i = imag)
  ```

  不过，对于带有变量注释的默认参数，**一定**要在等号 `=` 周围使用空格：

  合适：

  ```python
  def munge(sep: AnyStr = None): ...
  def munge(input: AnyStr, sep: AnyStr = None, limit=1000): ...
  ```

  不合适：

  ```python
  def munge(input: AnyStr=None): ...
  def munge(input: AnyStr, limit = 1000): ...
  ```

- 通常不建议使用符合语句（将多条语句写在同一行）。

  合适：

  ```python
  if foo == 'blah':
      do_blah_thing()
  do_one()
  do_two()
  do_three()
  ```

  最好不要：

  ```python
  if foo == 'blah': do_blah_thing()
  do_one(); do_two(); do_three()
  ```

- 然而有的时候，把一条简短的 if/for/while 语句放在一行是可以的。对于多子句语句，永远不要这样做。同时要避免合并比较长的行。

  最好不要：

  ```python
  if foo == 'blah': do_blah_thing()
  for x in lst: total += x
  while t < 10: t = delay()
  ```

  绝对不要：

  ```python
  if foo == 'blah': do_blah_thing()
  else: do_non_blah_thing()
  
  try: something()
  finally: cleanup()
  
  do_one(); do_two(); do_three(long, argument,
                               list, like, this)
  
  if foo == 'blah': one(); two(); three()
  ```

# 何时使用收尾逗号

收尾逗号通常是可写可不写的，除了在只有一个元素的元组中（而且在 Python 2 的 `print` 语句中有特殊语义）。为了能表达清楚，还建议使用括号将后面的情况括起来（这在技术上是冗余的）。

合适：

```python
FILES = ('setup.cfg',)
```

可以，但是比较迷惑：

```python
FILES = 'setup.cfg',
```

当列表中的值、参数或导入的项目慢慢扩展时，冗余的收尾逗号，常常对版本控制系统很有用。这种模式把每一个值（等）本身作为一行插入，总是增加一个收尾逗号，并且在下一行增加回圆括号/方括号/大括号。不过对于写在一行的定界符就没有必要使用收尾逗号了（上面说的单元素元组除外）。

合适：

```python
FILES = [
    'setup.cfg',
    'tox.ini',
    ]
initialize(FILES,
           error=True,
           )
```

不合适：

```python
FILES = ['setup.cfg', 'tox.ini',]
initialize(FILES, error=True,)
```

# 注释

与代码相矛盾的注释还不如没有。每次更改代码，最想想到的必须是更新注释！

注释应该是完整的句子。第一个单词的首字母要大写，除非它是一个以小写字母开头的标识符（永远不要改变标识符的大小写！）。

块注释通常由一段或多段完整的句子构成，每个完整的句子都要以句号结尾。

在多个句子构成的注释中，除了最后一句之外，每一句的结尾句号之后都要加两个空格。

书写英语时，请遵守 Strunk and White[^12] 书写规范

那些非英语国家的程序员们：请用英语写注释，除非你 120% 确定你的代码不会被不懂你的语言的人使用。

## 块注释

块注释通常和它后面的一些（或全部）代码有关，应该与那些相关的代码处于同一级缩进。块注释的每一行都以一个井字符 `#` 和一个空格开头（除非它是注释内的缩进文本）。

每一段行内注释之间以一个只包含一个井字符 `#` 的空行分隔开。

## 行内注释

尽量少用行内注释。

行内注释指的是与某条语句出在同一行的注释。行内注释与程序语句之间至少要用两个空格分隔。行内注释应该以一个井字符 `#` 和一个空格开头。

行内注释是不必要的。而且如果它仅仅是一句废话的话，就很容易让人分心了。不要做这种事：

```python
x = x + 1                 # 给x加一
```

不过有时候，这样的注释很有用：

```python
x = x + 1                 # 补偿边界
```

## 文档字符串

书写规范的文档字符串[^13]的规范被记载在 [PEP 257](https://www.python.org/dev/peps/pep-0257) 中。

- 应该给所有的公共模块、函数、类和方法写文本字符串。对于非公共方法是不必写文档字符串的，但是你应该使用注释来描述那些方法到底是做什么的。这种注释应该出现在 `def` 下面的那一行

- [PEP 257](https://www.python.org/dev/peps/pep-0257) 描述了好的文档字符串规范是什么样的。千万注意，多行文档字符串最后的三个双引号 `"""` 应该但独占一行：

  ```python
  """Return a foobang
  Optional plotz says to frobnicate the bizbaz first.
  抱歉，这一段话翻译不出来...
  """
  ```

- 至于单行文档字符串，请把三个回引号 `"""` 也放在那一行。

# 命名规范

Python 库的命名规范有一点乱，所以我们永远不可能对此达成共识。尽管如此，这里还是提供了当前推荐的明明标准。新的模块和包（包括第三方框架）应该按照这些标准来写。但是对于那些使用了不同格式的已有的库，还是倾向于与其内部使用的规则保持一致。

## 最高原则

用户可见的公共 API 接口，应该按照能够反映出用途的方式命名，而非一定按照规则来。

## 描述性：命名样式

有很多不同的命名样式。能够通过命名样式大致了解一个名称的功能，会很有帮助。

命名样式通常分为下面这些类：

- `b` （单个小写字母）

- `B` （单个大写字母）

- `lowercase` （小写字母）

- `lower_case_with_underscores` （带下划线的小写字母）

- `UPPERCASE` （大写字母）

- `UPPER_CASE_WITH_UNDERSCORES` （带下划线的大写字母）

- `CapitalizedWords` （每个单词首字母大写，或者叫标题词，也叫**驼峰体** -- 之所以这么称呼是因为它起伏不平的单词外观就像骆驼的驼峰一样[^4]）。这种样式也被称作“猛男体”。

  注意：对于首字母缩写的单词，如果要用到驼峰体，那么每个缩写的首字母都要大写。因此 `HTTPServerError` 这种写法要比 `HttpServerError` 更恰当。

- `mixedCase` （混合使用大小写，与首字母大写的驼峰体不同的是，它的第一个字母是小写的！）

- `Capitalized_Words_With_Underscores` （带下划线的首字母大写，丑！）

还有一种样式，使用一个独特的前缀来把相关的名称分组。这种样式在 Python 中并不常见，但是为了完整起见，还是在这里提上一嘴。例如， `os.stat()` 函数的返回值是一个元组，它的元素按传统呈现 `st_mode`、`st_size`、`st_mtime` ，等等的样子。（这样做主要是为了和计算机环境可移植操作系统接口，POSIX系统的格式保持一致，这会帮助程序员更容易熟悉它的用法。）

X11库在它所有的公共方法中都加上了前缀X。众所周知，在 Python 中，这种样式是没有必要的。因为属性和方法的名字会以对象名为前缀，函数名会以模块名为前缀。

另外，下面这些使用下划线开头或结尾的样式值得好好看看（这些样式可以和任何一种大小写样式结合使用）：

- `_single_leading_underscore`：单下划线开头的是弱“内部使用”标识符。例如，`from M import *` 不会导入以单下划线开头的名称。

- `single_trailing_underscore_`：单下划线结尾的名称习惯上用来避免和 Python 关键字冲突，例如

  ```python
  Tkinter.Toplevel(master, class_='ClassName')
  ```

- `__double_leading_underscore`：双下划线开头的变量名用来明明类属性，会引发名称校正（在 FooBar 类中， `__boo` 会变成 `_FooBar__boo`，参见下面的内容）。

- `__double_leading_and_trailing_underscore__`：以双下划线开头和结尾的是存在于用户操控的名称空间的魔法对象或方法。例如： `__init__`、`__import__` 或 `__file__`。永远不要自己创造这类名称，一定按照文档说明使用它们。

## 约定俗成：名称传统

### 避免使用的名称

永远不要用`l`（L 的小写字母）、`O`（o 的大写字母）或 `I` （i 的大写字母）作为单字符变量名。

有些字体的这些字母很难和数字一和零区分。如果要使用`l`，请使用 `L` 代替。

### ASCII 兼容

标准库中的标识符必须兼容 ASCII，就像在 [PEP 3131](https://www.python.org/dev/peps/pep-3131) 的 [方针部分](https://www.python.org/dev/peps/pep-3131/#policy-specification) 中描述的那样。

### 包和模块名

模块名应该全部小写，并且越短越好。如果能提高可读性的话，可以在模块名中加下划线。Python 包的名字也应该全部消息，越短越好。不过还是断了在包的名字中使用下划线的念头吧。

使用 C 或 C++ 写的扩展模块有一个伴随的 Python 模块提供高级（例如，更加面向对象）接口，这些 C/C++ 模块名要以但下划线开头（例如， `_socket`）。

### 类名

类名通常使用驼峰体。

方法的命名规则可能要使用特定文档要求的接口名称，以确保能够被正确调用。

需要注意的是，内置方法名有不同的样式：大多数内置方法使用单个单词（或者两个合在一起的单词），驼峰体样式的方法名仅仅在一些例外和内置常数中使用。

### Type变量名

在 [PEP 484](https://www.python.org/dev/peps/pep-0484) 中引入的 type 变量通常使用简写的驼峰体名称：`T`、`AnyStr`、`Num`。声明具有相同（covariant）或相反（contravariant）变化趋势的变量名时，建议在变量名后相应地加上 `_co` 或 `_contra` 后缀：

```python
from typing import TypeVar

VT_co = TypeVar('VT_co', covariant=True)
KT_contra = TypeVar('KT_contra', contravariant=True)
```

### 异常类名

因为异常一般是类，所以应该遵循类的命名规则。只是，应该在在异常的名字前加上“Error”前缀（如果异常确实是一个错误的话）。

### 全局变量名

（让我们祈祷这些变量只用在一个模块内部。）全局变量的命名规范和函数的规则相同。

那些需要通过 `from M import *` 方式导入的模块，应该使用 `__all__` 方法来阻止输出全局变量。也可以使用古老些的规则，在这些全局变量的名字前加上一个下划线前缀（用来指明这些全局变量是“仅供本模块内部使用”的）。

### 函数和变量名

函数名应该全部小写。按照需要，使用下划线分隔每个单词，以增加可读性。

变量应该遵循与函数相同的命名规则。

混用大小写的变量名仅仅当代码中已经大量使用这种格式风格的时候（例如在 threading.py 中）使用，以保持向上的兼容性。

### 函数和方法的参数

永远使用 `self` 作为实例方法的第一个参数。

永远使用 `cls` 作为类方法的第一个参数

如果某个函数的参数名与 Python 中预留关键字冲突，通常最好在它后面加一个但下划线尾巴，而不是使用缩写或错误拼写。因此，`class_` 要比好。（或者更好的办法是使用同义词来避免这样的冲突。）

### 方法名和实例变量

使用函数命名规则：全部小写字母，在必要地方使用下划线分隔以提高可读性。

在非公共的方法或实例变量前使用但下划线开头。

为了避免和子类中的属性重名，使用双下划线开头来启用 Python 的命名校正规则。

Python 会用类名对这些属性名进行校正：如果 Foo 类有一个名为 `__a` 的属性，我们无法在外部通过 `Foo.__a` 方式调用它。（某个固执的朋友或许仍然可以通过调用 `Foo._Foo__a` 来访问该属性）。通常，双下划线开头的名称只用来避免和子类中的属性名冲突。

注意：关于如何使用双下划线开头的名字有些争议（下面有讨论）。

### 常量

常量通常在模块级的位置定义，全部字母大写，以下划线分隔每个单词。例如：`MAX_OVERFLOW` 和 `TOTAL`。

### 继承设计

一定要搞清楚类中的方法和实例变量（统称“属性”）到底应该是共有的还是非公有的。如果不确定的话，把它视为非公有的。把非公有属性变成公有属性要比把共有属性变成非公有的容易些。

公有属性指的是你想要让一些其他不相关的类也可以使用的属性，你需要确保避免与向上兼容性冲突的改变。非公有属性指的是你不打算让第三方使用的属性。你不必要求私有属性不能改变，甚至你把它删除掉也没有关系。

在这里我们没有使用“私有”这个术语，因为在 Python 中没有哪个属性是真正私有的（不必做无用功）。

还有一种部分属于“子类 API”的属性（在其他语言中，通常被称作“被保护的”）。一些类被设计成能够供其他类继承的形式，来扩展或装饰类的功能。在设计这种类时，仔细区分哪些属性该是共有的，也就是子类 API 的一部分，以及哪些类真的只会在基类中使用。

牢记这些 Pythonic 的规则：

- 公有属性不应该以下划线开头。

- 如果你的公有属性名和预留关键字冲突，在你的属性名后面加上一个下划线结尾。这比使用缩写或错误拼写更合适（尽管如此，“cls”是变量或参数比较恰当的拼写，因为大家都知道它表示一个类，尤其是作为类方法的第一个变量时）

  注意 1：阅读前文了解类方法参数的命名建议。

- 对于简单的公有数据属性，最好直接使用属性名，而不是复合调用者/扩展者的方法名。不要忘了 Python 程序未来扩展起来是十分方便的。不久的将来，你很可能会给这些数据进行扩展，这样方法名会越来越长，你得为此提前做好准备。在那时，使用简单地数据属性接口语句代替其背后复杂的程序过程。

  注意 1：属性仅适用于新式类中。

  注意 2：尽量保证函数表现得没有副作用，尽管像缓存存储之类的副作用通常是无妨的。

  注意 3：避免用属性进行计算密集型的操作。属性往往会让调用者先入为主地以为访问它（相对而言）不会占用很多资源。

- 如果你要定义的类可能会有子类，而且这个类里面有某些属性你不希望被子类继承，可以考虑使用双下划线开头来命名这些属性，不过不要同时加结尾的双下划线。这样做会引发 Python 的名称校正算法，属性名会被用类名进行校正。这种校正帮助我们避免子类无意间包含与父类中同名的方法而造成的名称冲突。

  注意 1：校正后的名字其实只是把类名简简单单放到属性名前面。所以如果子类的类名和方法名都和父类相同，名称冲突还是会存在的。

  注意 2：名称校正在真正使用的时候，比如调试和 `__getattr__()`，可能会不太方便。好在名称校准的算法已经很明确且完善了，手动推导也是很容易的。

  注意 3：并非所有人都喜欢名称校正。尽量在避免意外的名称冲突和潜在的广大调用者的喜好之间找到平衡。


## 公共和内部接口

任何的向上兼容性的保证仅适用于公共接口。于是，让用户能够清楚区分公共和内部接口是很重要的。

文档中提及的接口被认为是公共接口，除非文档明确声明它们是从通常的向上兼容保证中豁免的临时的或内部接口。所有文档中未提及的接口都应该被假定为内部接口。

为了提高可读性，模块应该使用 `__all__`属性明确声明它们公共 API 的名称。设置 `__all__` 为一个空列表表明该模块没有公共 API。

即便已经设置妥当，内部接口（包、模块、类、函数、属性或其他的名称）名称仍应该使用单下划线开头。

内部接口命名空间（包、模块或类）里面的接口也被视为内部属性。

一定要给导入的模块名斟酌一个详细的操作说明。其他模块一定不能依赖于间接访问这些模块名，除非它们是包含于这些模块 API 中的明文指出的部分，比如在 `os.path` 或一个包的 `__init__` 模块中公开功能的子模块。

# 编程建议

- 代码的编写不应该阻碍其他 Python 操作的执行（PyPy、Jython、IronPython、Cython、Psyco，诸如此类）。

  例如，不要依赖 CPython 中高效的原地字符串拼接操作，也就是 `a += b` 或 `a = a + b` 这种表达式。这种简便写法即便在 CPython 中也是容易出错的（只在某些情况有效），在不使用引用计数的操作中干脆不能使用。在标准库中对性能比较敏感的部分，应该使用 `''.join()` 格式代替上面说的字符串拼接方法。这种格式能确保在不同的操作中，字符串的拼接都能稳定执行。

- 与单个数值例如 None 作比较时，永远要使用 `is` 或者 `is not` 来完成，而不是使用等号比较。

  同时，如果你想要表达 `if x is not None`，却直接把表达式写成 `if x`的时候，你可要当心了。比如，在测试一个默认值为 None 的变量或参数是否被赋予其他值的时候。其他值也可能是布尔属性为假的数据类型（比如空的容器）！

- 使用 `is not` 运算符而不是 `not ... is`。尽管两个表达式在功能上没有分别，但是前一种写法可读性更高，也更广为接受。

  合适：

  ```python
  if foo is not None:
  ```

  不合适：

  ```python
  if not foo is None:
  ```

- 当进行比较密集型的排序操作时，最好补齐测试全部六种比较运算符（`__eq__`、`__ne__`、`__lt__`、`__le__`、`__gt__`、`__ge__`），而不是依赖于其他代码而仅练习某一种比较。

  `functools.total_ordering()` 装饰器提供一个生成缺失的比较方法的工具，使用它可以最大限度地减少操作量。

  [PEP 207](https://www.python.org/dev/peps/pep-0207) 指出，Python *确实* 采用了反身性规则。因此，解释器或许会将 `y > x` 反转为 `x < y`，`y >= x` 反转为 `x <= y`。而且，也许会反转 `x == y` 和 `x != y` 语句。在 `sort()` 和 `min()` 操作中使用 `<` 运算符，以及在 `max()` 函数中使用 `>` 运算符是很保险的。不过，最好还是测试全部六种运算符，以确保其他上下文语句不会产生异常。

- 如果想要将 lambda 表达式直接和标识符关联，一定要使用 def 语句而不是赋值语句。

  合适：

  ```python
  def f(x): return 2*x
  ```

  不合适：

  ```python
  f = lambda x: 2*x
  ```

  第一种格式意味着新得到的函数对象的名字是明确的“ f ”，而不是笼统的“\<lambda\>”。通常，这对于回溯调试和使用字符串描述都很有用。使用赋值语句丧失了一个 lambda 表达式相对于明确的 def 语句最核心的优点（比如，它能嵌套进其他表达式的内部）。

- 通过继承 `Exception` 类来派生异常，而不是`BaseException` 类。直接继承自 `BaseException` 的类被预留给那些差不多永远也不应该被捕获的异常。

  设计异常继承关系应该基于使用代码捕获到需要进行的改进操作，而不是仅仅找到异常抛出的位置。尽量有条理地回答“发生了什么错误”的问题，而不是隔靴搔痒般地陈述“出现了一个问题”（参见 [PEP 3151](https://www.python.org/dev/peps/pep-3151)，有一个从内置的异常继承关系中学到的关于此的例子）。

  类的命名规则在此是使用的，而且如果异常是一个错误，应该给其加上“Error”前缀。用来进行非局部流控制或其他类型的信号之类的非错误异常无需加特别地前缀。

- 合理使用异常链。在 Python 3 中，`raise X from Y` 应该被用来表示明确的不会丢失原始错误信息回溯的替换。

  经过深思熟虑后，当真需要替换内部异常（在 Python 2 中使用 `raise X` 或者在 Python 3.3+ 中使用 `raise X from None`）的时候，留意转换成新异常过程中的细节（比如当将 `KeyError` 替换成 `AttributeError` 时，不要忘了保留属性名，同时要把原来的错误文本替换成新的异常信息）。

- 在 Python 2 中抛出异常时，使用 `raise ValueError('message')` 格式，而不是过时的 `raise ValueError, 'message'`。

  后者在 Python 3 的语法中是不合法的。

  使用括号的形式也意味着，当异常陈述很长，或者包含了字符串的格式化，你不必把它们全都写在一行。因为括号的存在，换行会很方便。

- 捕获异常时，尽可能指出明确的异常，而不是直接使用 `except:` 子句：

  ```python
  try:
      import platform_specific_module
  except ImportError:
      platform_specific_module = None
  ```

  直接使用 `except:` 子句也会捕捉到 `SystemExit` 和 `KeyboardInterrupt` 异常，这样以来就无法使用 `Control-C` 终止程序，同时也会掩饰掉其他的麻烦。如果你想要捕获所有意味着程序错误的错误，使用 `except Exception:`（直接使用 except 等价于 `except BaseException:`）。

  把直接使用“except”子句限制在两种情况是值得大拇指表扬的好习惯：

  1. 如果一场句柄会被打印出来或者回溯信息会被日志记录下来，至少用户能够知道错误是发生了的。
  2. 如果代码需要一些清理工作，但是密集使用 `raise` 抛出异常。使用 `try...finally` 应对这种情况比较合理的方式。

- 耦合捕获异常时，最好使用 Python 2.6 中加入的明确的名称耦合语法：

  ```python
  try:
      process_data()
  except Exception as exc:
      raise DataProcessingFailedError(str(exc))
  ```

  这是 Python 3 中唯一支持的语法。与旧版的基于逗号的语法相比，这种语法避免了模棱两可的问题。

- 捕获操作系统错误时，在指出 `errno` 数值的同时，最好使用 Python 3.3 中引入的明确的异常继承关系。

- 此外，对于所有的 try/except 子句，在满足需求的情况下，保证 `try` 子句中的代码尽可能的短。再次强调，这可能会错过隐藏的 bug。

  合适的写法：

  ```python
  try:
      value = collection[key]
  except KeyError:
      return key_not_found(key)
  else:
      return handle_value(value)
  ```

  不合适的写法：

  ```python
  try:
      # 太宽泛了！
      return handle_value(collection[key])
  except KeyError:
      # 也会捕获到 handle_value() 抛出的 KeyError
      return key_not_found(key)
  ```

- 当某个资源仅在某一个特定部分的代码局部使用时，使用 `with` 语句确保它及时可靠地被清理掉了。使用 try/finally 语句也是可以接受的。

- 如果除了获取和释放资源还要处理其他的任务，就应该使用不同的函数或方法调用上下文管理器。

  合适的写法：

  ```python
  with conn.begin_transaction():
      do_stuff_in_transaction(conn)
  ```

  不合适的写法：

  ```python
  with conn:
      do_stuff_in_transaction(conn)
  ```

  没有任何信息表明后一个例子的和方法除了事务结束后关闭连接还做了其他的事。在这种情况下，要尽可能的明确。

- return 语句要保持一致。要么一个函数中所有的 return 语句都返回一个表达式，要么都不这么做。如果任何一个 return 语句返回一个表达式，任何没有值的 return 语句都应该明确陈述成 `return None`，而且一个明确的 return 语句应该在函数的末尾出现（如果可行的话）。

  合适的写法：

  ```python
  def foo(x):
      if x >= 0:
          return math.sqrt(x)
      else:
          return None
  
  def bar(x):
      if x < 0:
          return None
      return math.sqrt(x)
  ```

  不合适的写法：

  ```python
  def foo(x):
      if x >= 0:
          return math.sqrt(x)
  
  def bar(x):
      if x < 0:
          return
      return math.sqrt(x)
  ```

- 使用字符串方法而不是字符串模块。

  字符串方法总是快很多，而且和 unicode 字符串使用相同的 API。如果需要向前兼容 Python 2.0 之前的版本的话，无视这条规则。

- 使用 `''.startswith()` 和 `''.endswith()` 而不是字符串切片检查前缀或后缀。`startswith()` 和 `endswith()` 更加简洁而且更不易出错：

  ```python
合适:   if foo.startswith('bar'):
  不合适:  if foo[:3] == 'bar':
  ```
  
- 使用 `isinstance()` 比较对象类型，而不是直接比较类型。

  ```python
  合适:   if isinstance(obj, int):
  不合适:  if type(obj) is type(1):
  ```

  当判断一个对象是否是字符串时，不要忘了它也有可能是 unicode 字符串！在 Python 2 中，`str` 和 `unicode` 有一个共同的基类，`basestring`。所以可以这样做：

  ```python
  if isinstance(obj, basestring):
  ```

  注意在 Python 3 中，`unicode` 和 `basestring` 不复存在了（只有 `str`）而且字节对象不再是一种字符串（不过它是一串数字）。

- 对于序列（字符串，列表，元组），不要忽略空序列代表假。

  ```python
  合适:    if not seq:
          if seq:
  
  不合适:  if len(seq):
          if not len(seq):
  ```

- 不要在字符串的末尾过度使用空格。这种结尾的空格视觉上是不可见的，而且有些编辑器（或者更常见的，使用 reindent.py）会把它们直接切掉。

- 不要使用 `==` 与 True 或 False 比较布尔值。

  ```python
  合适:   if greeting:
  不合适: if greeting == True:
  更糟糕: if greeting is True:
  ```

- 不要在 `try...finally` 套件中使用流程控制语句 `return`/`break`/`continue`，这些流程控制语句可能会跳出 finally 套件。因为这些语句会隐藏掉 finally 套件中活跃的异常。

  不要这样做：

  ```python
  def foo():
      try:
          1 / 0
      finally:
          return 42
  ```

## 函数注释

为了和 [PEP 484](https://www.python.org/dev/peps/pep-0484) 兼容，函数注释的格式规则正在被修订。

- 为保证向前的兼容性，Python 3 代码中的函数注释最好使用 [PEP 484](https://www.python.org/dev/peps/pep-0484) 语法。（前面部分有一些关于注释的格式建议。）

- 本 PEP 从前建议的注释格式的尝试不再提倡。

- 然而，除了标准库之外，现在更提倡 [PEP 484](https://www.python.org/dev/peps/pep-0484) 中的规则尝试。例如，使用 [PEP 484](https://www.python.org/dev/peps/pep-0484) 格式书写注释标记大量的第三方库或应用，就知道使用这些注释是多么简单了，而且这种格式的代码更加容易理解。

- Python 标准库不应使用这种注释。但是对于新的代码和大的代码重构来说是允许使用的。

- 对于想要使用函数注释进行不同用途的代码，建议再上一个陈述类型的注释：

  ```python
  # type: ignore
  ```

  靠近文件的最顶部。这告诉类型检查器忽略掉所有的注释。（规避类型检查器更加精细的方法可以在 [PEP 484](https://www.python.org/dev/peps/pep-0484) 中找到。）

- 像轧毛机一样，类型检查器是可选的，独立的工具。默认的 Python 解释器不应因类型检查而抛出信息，而且也不应该基于注释选择它们的行为。

- 不想使用类型检查器的用户大可无视它们。然而，对于使用需要在包中运行类型检查器的第三方库包的用户来说，还是需要遵守规则的。对于这种情况，建议使用存根文件：.pyi 文件比 .py 文件更受类型检查器青睐。存根文件可以使用一个库分发，也可以用 typeshed repo[^5] 分解（在库作者的许可下）。

- 对于需要向上兼容的代码，类型注释可以以单行注释的形式加入。参见中 [PEP 484](https://www.python.org/dev/peps/pep-0484) [^6]的相关部分。

## 变量注释

[PEP 526](https://www.python.org/dev/peps/pep-0526) 介绍了变量注释。它们的格式建议和上面谈到的函数注释很像：

- 模块级变量，类和实例变量以及局部变量的注释，应该在冒号后面留一个空格。

- 冒号前不应该有空格。

- 如果右手边有赋值式，等号两边都要有且只有一个空格。

- 合适的写法：

  ```python
  code: int
  
  class Point:
      coords: Tuple[int, int]
      label: str = '<unknown>'
  ```

- 不合适的写法：

  ```python
  code:int  # 冒号后没有空格
  code : int  # 冒号前有个空格
  
  class Test:
      result: int=0  # 等号两边没有空格
  ```

- 虽然 [PEP 526](https://www.python.org/dev/peps/pep-0526) 是适配于 Python 3.6 版本的，这些变量注释语法是所有版本 Python 存根文件的首选语法（更多细节，参见 [PEP 484](https://www.python.org/dev/peps/pep-0484)）。

# 版权

原文档无版权限制，翻译文档亦供大家随意取用，引用尽量注明出处。

文档来源： https://github.com/python/peps/blob/master/pep-0008.txt

# 参考文献及译注

[^1]: [PEP 7](https://www.python.org/dev/peps/pep-0007)，C 语言代码格式规则，van Rossum
[^2]: Barry 的 GNU Mailman 格式规则 http://barry.warsaw.us/software/STYLEGUIDE.txt
[^3]: Donald Knuth 的 *The TeXBook*，195 和196 页
[^4]: http://www.wikipedia.com/wiki/CamelCase    
[^5]: Typeshed repo https://github.com/python/typeshed
[^6]: Suggested syntax for Python 2.7 and straddling code https://www.python.org/dev/peps/pep-0484/#suggested-syntax-for-python-2-7-and-straddling-code

[^7]:悬挂缩进是一种段落格式。在悬挂缩进的一段话中，除第一行外，其他每一行都缩进相同距离。在 Python 语境中，这种情况表示的是，第一行最后一个非空白字符后暗含一个左括号。括号中的内容在下面悬挂缩进，直到括号中的内容结束。最后一行的最后一个非空白字符后暗含一个右括号。
[^8]: dummy implementations
[^9]: overwriting a pure Python implementation Python of an interface with the definitions from an optional accelerator module and exactly which definitions will be overwritten isn't known in advance
[^10]: Pet Peeves
[^11]: augmented assignment
[^12]: The Elements of Style。维基百科连接：https://en.wikipedia.org/wiki/The_Elements_of_Style；百度文库连接：https://wenku.baidu.com/view/982a59620b1c59eef8c7b40c.html
[^13]: documentation strings（亦称作：docstrings） 