## for 循环

与以死循环形式存在的 `while 循环` 不同的是，`for 循环` 往往以有限循环的形式存在。

`for 循环` 的基本结构为：

```python
for i in XXX:
    循环体
```

其中，

- `for`：关键字
- `i`：变量名
- `in`：关键字
- `xxx`：可迭代对象

用已有的知识，如果我们想分别打印字符串 `"alex"` 中的每一个字符，可以使用 `while循环` 来实现：

```python
name = 'alex'
count = 0
while count < len(name):
    print(name[count])
    count += 1
```

这里补充一个知识点，函数 `len()` 是一个公共方法，它可以获得传入参数的长度：

```python
>>> name = "alex"
>>> len(name)
4
```

如果使用 `for循环`，我们可以更简便地实现目的：

```python
name = "alex"
for i in name: # 每次循环，for都会把取到的元素赋值给i
    print(i) # 打印变量i
```

看下面这样一个例子：

```python
for i in "abcde":
    pass
print(i)
```

最终打印出来的结果只有一个 `e`。那是因为 `for 循环` 本质是一个 `赋值` 操作，每次循环都是将可迭代对象中的一个元素 `赋值` 给变量。当进行最后一次循环时，字符串 "abcde" 的最后一个元素 "e" 被赋值给了变量 i。循环结束，i 没有被重新 `赋值`。虽然打印的动作不在循环体中，但不影响打印出 `e` 的结果。

for 循环打印九九乘法表：

```python
for i in range(1, 10):
    for j in range(1, 10):
        if i >= j:
            print(f"{j} * {i} = {i * j}", end=' ')
    print('')
```

思考题：

下面的代码会打印出什么样的结果呢？

```python
num = 5
count = 1
while num:
    for i in "abc":
        print(i + str(count))
    count += 1
    num -= 1
```
