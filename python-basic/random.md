## random 模块

random 模块，也就是随机数模块，用来生成一定范围内的随机数。随机数模块可以这样使用：

```python
import random
print(random.random())    # 用来生成0~1之间的小数
print(random.randint(1, 5))   # 随机生成1~5之间（包括1和5）的整数
print(random.randrange(0, 10, 2))    # 随机产生range切片范围内的整数，参数的使用方法与range相同

lst = [1, 2, 3, 4, 5, 6, 7, 8, 9]
print(random.choice(lst))    # 从可迭代对象中随机选出一个元素
print(random.choices(lst, k=5))    # 从可迭代对象中选择指定个数的元素，可能会重复选取
print(random.sample(lst, k=5))    # 从可迭代对象中选取指定个数的元素，不会重复选取
random.shuffle(lst)    # 将列表顺序打乱
print(lst)
```

输出的结果为：

```python
0.30540264424342467
2
6
4
[3, 1, 9, 3, 8]
[8, 5, 9, 6, 3]
[3, 5, 1, 8, 4, 6, 9, 7, 2]
```