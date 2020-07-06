## re 模块和正则表达式

[TOC]

### re 模块概述

re，也就是正则表达式，用来从字符串中获取我们想要的内容：

```python
import re
a = 'alex,meet,eva_j'
print(re.findall('e', a))    # findall放来的两个参数为：参数1用来传入要查找的内容，参数2用来传入原来的字符串
```

输出的结果为：

```python
['e', 'e', 'e', 'e']
```

### 元字符

正则表达式的当然不仅仅能查找这种简单地字符。正则表达式真正的强大之处在于它无所不包的匹配规则。这一套匹配规则对于所有语言来说都是通用的，通过一个个元字符组合而成：

|   元字符    |                           匹配内容                           |
| :---------: | :----------------------------------------------------------: |
|     \w      |              匹配字母（包括中文）、数字或下划线              |
|     \W      |        匹配除字母（包括中文）、数字或下划线以外的字符        |
|     \s      |                       匹配任意空白字符                       |
|     \S      |                      匹配任意非空白字符                      |
|     \d      |                         匹配任意数字                         |
|     \D      |                      匹配任意非数字字符                      |
|    \A或^    | 从字符串的开头匹配。若指定 re.MULTILINE 或 re.M，则从字符串开头或换行符后匹配 |
|    \Z或$    | 从字符串的结尾匹配。若指定 re.MULTILINE 或 re.M，则从字符串结尾或换行符前匹配 |
|      .      | 匹配任意字符，除了换行符。当 re.DOTALL 或 re.S 标记被指定时，则可以匹配包括换行符的任意字符 |
| [0-9a-zA-Z] |      用于匹配特定范围的数字和字母，中间不能有空格和逗号      |
|      *      |  匹配 0 个或多个左边的字符，贪婪匹配（匹配尽可能长的内容）   |
|      +      |             匹配 1 个或多个左边的字符，贪婪匹配              |
|      ?      | 匹配 0 个或 1 个左边的字符，非贪婪匹配。也可以用来将 * 和 + 转换为非贪婪匹配模式 |
|     {n}     |                   精准匹配 n 个前面的字符                    |
|   {n, m}    |            匹配 n 到 m 个前面的字符片段，贪婪匹配            |
|    a\|b     |                     a 或者 b，优先匹配 a                     |
|     ()      |               匹配括号内的表达式，也表示一个组               |

注：若 `re.MULTILINE` 和 `re.DOTALL` 需要同时指定，可以使用并集，即 `flags=re.DOTALL | re.MULTILINE`

正则表达式的使用方法为：

```python
s = 'alex1, 哈啰，123,meet!@'
print(re.findall('\w', s))    # 匹配数字字母（包括中文）字符串
print(re.findall('\W', s))
输出的结果为：
['a', 'l', 'e', 'x', '1', '哈', '啰', '1', '2', '3', 'm', 'e', 'e', 't']
[',', ' ', '，', ',', '!', '@']

s1 = 'alex w \n, dal\t,sdjfl'
print(re.findall('\s', s1))    # 匹配空白字符
print(re.findall('\S', s1))    # 匹配非空白字符
输出的结果为：
[' ', ' ', '\n', ' ', '\t']
['a', 'l', 'e', 'x', 'w', ',', 'd', 'a', 'l', ',', 's', 'd', 'j', 'f', 'l']

s2 = 'a1b2c3'
print(re.findall('\d', s2))    # 匹配所有数字
print(re.findall('\D', s2))    # 匹配所有非数字
输出的结果为：
['1', '2', '3']
['a', 'b', 'c']

s3 = 'alex'
print(re.findall('\Aa', s3))    # 从开头匹配
print(re.findall('^a', s3))
print(re.findall('x\Z', s3))    # 从结尾匹配
print(re.findall('x$', s3))
输出的结果为：
['a']
['a']
['x']
['x']

s4 = 'abc a12\n3!@\t#中文_'
print(re.findall('.', s4))    # 匹配任意字符（换行符除外）
print(re.findall('\n', s4))
print(re.findall('.', s4, re.DOTALL))    # 匹配包括换行符之内的任意字符
输出的结果为：
['a', 'b', 'c', ' ', 'a', '1', '2', '3', '!', '@', '\t', '#', '中', '文', '_']
['\n']
['a', 'b', 'c', ' ', 'a', '1', '2', '\n', '3', '!', '@', '\t', '#', '中', '文', '_']

s5 = 'al ex()123!@'
print(re.findall('[0-9]', s5))    # 匹配范围内的数字
print(re.findall('[0-9a-zA-Z]', s5))    # 匹配范围内的数字和字母
输出的结果为：
['1', '2', '3']
['a', 'l', 'e', 'x', '1', '2', '3']

s6 = 'a.l e!x123'
print(re.findall('[.]', s6))    # 查找特定的字符“.”
print(re.findall('.', s6))    # 查找任意字符
print(re.findall('[^.]', s6))    # 查找除了“.”之外的所有字符
输出的结果为：
['.']
['a', '.', 'l', ' ', 'e', '!', 'x', '1', '2', '3']
['a', 'l', ' ', 'e', '!', 'x', '1', '2', '3']

s7 = 'alex-meet-leeek'
print(re.findall('e*', s7))    # 查找0个或多个字符e，贪婪匹配
print(re.findall('e+', s7))    # 匹配1个或多个字符e，贪婪匹配
print(re.findall('e?', s7))    # 匹配0个或1个字符e，非贪婪匹配
print(re.findall('e{2}', s7))   # 匹配刚好2个字符e
print(re.findall('e{1,2}', s7))    # 匹配1到2个字符2，贪婪匹配，打括号里面不能有空格
输出的结果为：
['', '', 'e', '', '', '', 'ee', '', '', '', 'eee', '', '']
['e', 'ee', 'eee']
['', '', 'e', '', '', '', 'e', 'e', '', '', '', 'e', 'e', 'e', '', '']
['ee', 'ee']
['e', 'ee', 'ee', 'e']

s8 = 'alex-meet-leeek'
print(re.findall('a|e', s8))   # 查找a或e，优先查找a
输出的结果为：['a', 'e', 'e', 'e', 'e', 'e', 'e']

s9 = 'alex-meeet-leeek'
print(re.findall('e(e)e', s9))    # 分组，对于findall方法只返回括号内的字符，对于search方法则返回整个表达式结构
print(re.search('e(e)e', s9).group())
print(re.findall('m(ee)e', s9))
print(re.findall('m(?:ee)e', s9))    # 分组，返回整个正则表达式结构
输出的结果为：
['e', 'e']
eee
['ee']
['meee']
```

### 正则匹配练习

接下来，我们就通过一些练习题，更加深入理解正则表达式的用法。

1. 有如下字符串：`'alex_sb ale123_sb wu12sir_sb wusir_sb ritian_sb 的 alex wusir '`，找到其中所有带 `_sb` 的内容。要求，输出结果中也要包含 `_sb`。

    ```python
    import re
    s = 'alex_sb ale123_sb wu12sir_sb wusir_sb ritian_sb 的 alex wusir '
    print(re.findall('\w[(?:.+?)]_sb', s))    # 第二个?用来让+变成非贪婪匹配
    print(re.findall('\w[a-z0-9]+_sb', s))
    print(re.findall('(\w.+?_sb) ', s))
    ```

    输出结果全都为：

    ```python
    ['alex_sb', 'ale123_sb', 'wu12sir_sb', 'wusir_sb', 'ritian_sb']
    ```

2. 有字符串 `"1-2*(60+(-40.35/5)-(-4*3))"`，
   1. 请找出字符串中的所有整数
   2. 请找出字符串中的所有数字（包含小数）
   3. 请找出所有的数字（正数、负数和小数）

    ```python
   import re
   s = "1-2*(60+(-40.35/5)-(-4*3))"
   print(re.findall('\d+', s))
   print(re.findall('\d+\.?\d*', s))
   print(re.findall('-?\d+\.?\d*', s))
    ```

    输出的结果为：

    ```python
   ['1', '2', '60', '40', '35', '5', '4', '3']
   ['1', '2', '60', '40.35', '5', '4', '3']
   ['1', '-2', '60', '-40.35', '5', '-4', '3']
    ```

3. 请找出字符串 `"http://blog.csdn.net/make164492212@163.com/article/details/51656638"` 中的邮箱。

    ```python
    import re
    s = "http://blog.csdn.net/make164492212@163.com/article/details/51656638"
    print(re.findall('\w+@\w+\.\w+', s))
    ```
    输出的结果为：
    ```python
    ['make164492212@163.com']
    ```

4. 请找出下列字符串中的
   1. 所有形如 `1995-04-27` 的时间
   2. 形如 `1980-04-27:1980-04-27` 的时间

    ```python
   import re
   s = '''
   时间就是1995-04-27,2005-04-27
   1999-04-27 老男孩教育创始人
   老男孩老师 alex 1980-04-27:1980-04-27
   2018-12-08
   '''
   print(re.findall('\d{4}-\d{2}-\d{2}', s))
   print(re.findall('(\S+:\S+)'))
    ```
   
    输出的结果为：
   
   ```python
   ['1995-04-27', '2005-04-27', '1999-04-27', '1980-04-27', '1980-04-27', '2018-12-08']
   ['1980-04-27:1980-04-27']
   ```

   

5. 匹配字符串 `"1231,11,2,1,-1,12.34545,abc,ssed"` 中的浮点数

    ```python
    import re
    s = "1231,11,2,1,-1,12.34545,abc,ssed"
    print(re.findall('\d+\.\d+', s))
    ```

    输出的结果为：
    
    ```python
    ['12.34545']
    ```

6. 匹配字符串 `"1231231,324233,123,1123,2435,1234,2546,23451324,3546354,13241234"` 中可能的 QQ 号（5 - 11 位）。

    ```python
    import re
    s = "1231231,324233,123,1123,2435,1234,2546,23451324,3546354,13241234"
    print(re.findall('\d{5,11}', s))
    ```

    输出的结果为：

    ```python
    ['1231231', '324233', '23451324', '3546354', '13241234']
    ```

7. 有下面一个长数据，请找出其中a标签中href后的网址（如 `http://www.cnblogs.com/guobaoyuan/articles/7087629.html`）。

    ```python
    import re
    msg = '''
    <div id="cnblogs_post_body" class="blogpost-body"><h3><span style="font-family: 楷体;">python基础篇</span></h3>
    <p><span style="font-family: 楷体;">&nbsp; &nbsp;<strong><a href="http://www.cnblogs.com/guobaoyuan/p/6847032.html" target="_blank">python 基础知识</a></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/p/6627631.html" target="_blank">python 初始python</a></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<strong><a href="http://www.cnblogs.com/guobaoyuan/articles/7087609.html" target="_blank">python 字符编码</a></strong></strong></span></p>
    <p><span style="font-family: 楷体;"><strong><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/articles/6752157.html" target="_blank">python 类型及变量</a></strong></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/p/6847663.html" target="_blank">python 字符串详解</a></strong></span></p>
    <p><span style="font-family: 楷体;">&nbsp; &nbsp;<strong><a href="http://www.cnblogs.com/guobaoyuan/p/6850347.html" target="_blank">python 列表详解</a></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/p/6850496.html" target="_blank">python 数字元祖</a></strong></span></p>
    <p><span style="font-family: 楷体;">&nbsp; &nbsp;<strong><a href="http://www.cnblogs.com/guobaoyuan/p/6851820.html" target="_blank">python 字典详解</a></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<strong> <a href="http://www.cnblogs.com/guobaoyuan/p/6852131.html" target="_blank">python 集合详解</a></strong></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/articles/7087614.html" target="_blank">python 数据类型</a>&nbsp;</strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/p/6752169.html" target="_blank">python文件操作</a></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/p/8149209.html" target="_blank">python 闭包</a></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/articles/6705714.html" target="_blank">python 函数详解</a></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/articles/7087616.html" target="_blank">python 函数、装饰器、内置函数</a></strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/articles/7087629.html" target="_blank">python 迭代器 生成器</a>&nbsp;&nbsp;</strong></span></p>
    <p><span style="font-family: 楷体;"><strong>&nbsp; &nbsp;<a href="http://www.cnblogs.com/guobaoyuan/articles/6757215.html" target="_blank">python匿名函数、内置函数</a></strong></span></p>
    </div>
    '''
    print(re.findall('href="(.+?)"'))
    ```

    输出的结果为：

    ```python
    ['http://www.cnblogs.com/guobaoyuan/p/6847032.html', 'http://www.cnblogs.com/guobaoyuan/p/6627631.html', 'http://www.cnblogs.com/guobaoyuan/articles/7087609.html', 'http://www.cnblogs.com/guobaoyuan/articles/6752157.html', 'http://www.cnblogs.com/guobaoyuan/p/6847663.html', 'http://www.cnblogs.com/guobaoyuan/p/6850347.html', 'http://www.cnblogs.com/guobaoyuan/p/6850496.html', 'http://www.cnblogs.com/guobaoyuan/p/6851820.html', 'http://www.cnblogs.com/guobaoyuan/p/6852131.html', 'http://www.cnblogs.com/guobaoyuan/articles/7087614.html', 'http://www.cnblogs.com/guobaoyuan/p/6752169.html', 'http://www.cnblogs.com/guobaoyuan/p/8149209.html', 'http://www.cnblogs.com/guobaoyuan/articles/6705714.html', 'http://www.cnblogs.com/guobaoyuan/articles/7087616.html', 'http://www.cnblogs.com/guobaoyuan/articles/7087629.html', 'http://www.cnblogs.com/guobaoyuan/articles/6757215.html']
    ```

### 正则表达式常用方法

除了 findall 外，正则表达式的常用方法还有 search，match 等：

```python
import re
s = 'alexmeet'
print(re.findall('e', s))
print(re.search('e', s))    # 从字符串任意位置进行匹配，查找到一个就停止，返回的是match对象，如果找不到则返回None
print(re.search('e', s).group())    # 将match对象转换为字符串
print(re.match('e', s))    # 从字符串的开头位置进行匹配，查找到返回match对象，查找不到返回None
print(re.match('a', s).group())
```

输出的结果为：

```python
['e', 'e', 'e']
<_sre.SRE_Match object; span=(2, 3), match='e'>
e
None
a
```

search 方法和 match 方法的比较：

- search 方法从字符串任意位置进行匹配，查找到一个就停止
- match 方法从字符串开始位置进行匹配，找不到返回 None

除了上面的几个方法，re 中还有一些不常用但也很有用的方法：

```python
import re
s1 = 'alex wusir,日天，太白;女神;肖锋：吴超'
print(re.split('[ :;,：；，]', s1))    # split分割
输出的结果为：
['alex', 'wusir', '日天', '太白', '女神', '肖锋', '吴超']

s2 = 'barry是最好的讲师，barry就是一个普通老师，请不要将barry当男神对待。'
print(re.sub('barry', 'meet', s2))    # sub替换
输出的结果为：
meet是最好的讲师，meet就是一个普通老师，请不要将meet当男神对待。

obj = re.compile('\d{2}')    # compile 定义匹配规则
print(obj.findall('alex12345'))
输出的结果为：
['12', '34']

it = re.finditer('e', 'meet,alex')    # 返回一个迭代器
print(it)
print(next(it))
print(next(it).group())
输出的结果为：
<callable_iterator object at 0x0000021E54F2AC50>
<_sre.SRE_Match object; span=(1, 2), match='e'>
e

s = '<h1>hello</h1>'
ret = re.search('<(?P<h>\w+)>(?P<h1>\w+)<(?P<h2>/\w+)>')    # 给分组取别名
print(ret.group('h'))
print(ret.group('h1'))
print(ret.group('h2'))
返回的结果为：
h1
hello
/h1
```

面试题：

1. 什么是贪婪匹配和非贪婪匹配？
2. search 和 match 的区别？