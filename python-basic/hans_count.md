## 使用 Python 统计中文字符的数量



方法一，排除法

假设只有中英文字符：

```python
import string

def str_count(str):
    '''找出字符串中的中英文、空格、数字、标点符号个数'''
    count_en = count_dg = count_sp = count_zh = count_pu = 0

    for s in str:
        # 英文
        if s in string.ascii_letters:
            count_en += 1
        # 数字
        elif s.isdigit():
            count_dg += 1
        # 空格
        elif s.isspace():
            count_sp += 1
        # 中文，除了英文之外，剩下的字符认为就是中文
        elif s.isalpha():
            count_zh += 1
        # 特殊字符
        else:
            count_pu += 1

    print('英文字符：', count_en)
    print('数字：', count_dg)
    print('空格：', count_sp)
    print('中文字符：', count_zh)
    print('特殊字符：', count_pu)


s = 'dfajl!大家@发！# 管道·符了3 54沙3发开fs\][dj'
str_count(s)
```

方法二，范围判断

Unicode 中，基本中文字符处在一个范围区间，可以参考 [汉字 Unicode 编码范围](https://www.qqxiuzi.cn/zh/hanzi-unicode-bianma.php)。写成代码就是：

```python
def hans_count(str):
    hans_total = 0
    for s in str:
        # 中文字符其实还有很多，但几乎都用不到，这个范围已经足够了
        if '\u4e00' <= s <= '\u9fef':
            hans_total += 1
    return hans_total

s = 'dfajl!大家@发！# 管道·符了3 54沙3发开fs\][dj'
print(hans_count(s))
```

参考资料：

1. [python统计中文字符数量](https://blog.csdn.net/xiamoyanyulrq/article/details/81504114)
2.  [汉字 Unicode 编码范围](https://www.qqxiuzi.cn/zh/hanzi-unicode-bianma.php)