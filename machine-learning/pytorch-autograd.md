# Pytorch 中的 autograd 自动求导

[toc]

神经网络训练学习的过程中，一个很重要很关键的步骤是反向传播和求导。在 Pytorch 框架中，通过一个 autograd package（自动求导工具包），提供了一个对 Tensors 上所有的操作进行自动求导的功能。

## 关于 torch.Tensor

torch.Tensor 是整个 package 中的核心类。如果将属性 `.requires_grad` 设置为 True，它将追踪在这个类上定义的所有操作。当代码要进行反向传播的时候，直接调用 `.backward()` 就可以自动计算所有的梯度。在这个 Tensor 上的所有梯度将被累加进属性 `.grad` 中。

如果想终止一个 Tensor 在计算图中的追踪回溯，只需要执行 `.detach()` 操作就可以将该 Tensor 从计算图中撤下。这样一来，在未来的回溯计算中也不会再计算该 Tensor。

除了 `.detach()`，如果想终止对计算图的回溯，也就是不再进行方向传播求导数的过程，还可以采用 `with torch.no_grad():` 代码块的方式。这种方式非常适用于对模型进行预测的时候，因为预测阶段不再需要对梯度进行计算。

## 关于 torch.Function:

Function 类是和 Tensor 类同等重要的一个核心类，它和 Tensor 共同构建了一个完整的类。每个 Tensor 的 `.grad_fn` 属性，代表它引用了哪个具体的 Function 创建了该 Tensor。

如果某个张量 Tensor 是用户自定义的，则其对应的 grad_fn is None。

## Tensor 的操作

```python
x1 = torch.ones(3, 3)
print(x1)

x = torch.ones(2, 2, requires_grad=True)
print(x)
```

输出结果：

```python
tensor([[1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.]])
tensor([[1., 1.],
        [1., 1.]], requires_grad=True)
```

在具有 `requires_grad=True` 的 Tensor 上执行一个加法操作：

```python
y = x + 2
print(y)
```

输出结果：

```python
tensor([[3., 3.],
        [3., 3.]], grad_fn=<AddBackward0>)
```

打印 Tensor 的 grad_fn 属性：

```python
print(x.grad_fn)
print(y.grad_fn)
```

输出结果：

```python
None
<AddBackward0 object at 0x000002270F102CD0>
```

在 Tensor 上执行更复杂的操作：

```python
z = y * y * 3
out = z.mean()
print(z, out)
```

输出结果：

```python
tensor([[27., 27.],
        [27., 27.]], grad_fn=<MulBackward0>) tensor(27., grad_fn=<MeanBackward0>)
```

关于方法 `.requires_grad_()`：该方法可以原地改变 Tensor 的属性 `.requires_gr`ad 的值。如果没有主动设定默认为 False：

```python
a = torch.randn(2, 2)
a = ((a * 3) / (a - 1))
print(a.requires_grad)
a.requires_grad_(True)
print(a.requires_grad)
b = (a * a).sum()
print(b.requires_grad)
print(b.grad_fn)
print(a)
print(b)
```

输出结果：

```python
False
True
True
<SumBackward0 object at 0x000002270F3A5EB0>
tensor([[-18.1909,   1.4176],
        [  1.5632,  -0.6163]], requires_grad=True)
tensor(335.7415, grad_fn=<SumBackward0>)
```

## 梯度 Gradients

在 Pytorch 中，反向传播是依靠 `.backward()` 实现的：

```python
out.backward()
print(x.grad)
```

输出结果：

```python
tensor([[4.5000, 4.5000],
        [4.5000, 4.5000]])
```

关于自动求导的属性设置：可以通过设置 `.requires_grad=True` 来执行自动求导，也可以通过代码块的限制来停止自动求导：

```python
print(x.requires_grad)
print((x ** 2).requires_grad)

# 实际使用时更推荐这种，不会改变外部的数据状态
with torch.no_grad():
    print((x ** 2).requires_grad)
```

输出结果：

```python
True
True
False
```

可以通过 `.detach()` 获得一个新的 Tensor，拥有相同的内容但不需要自动求导：

```python
print(x.requires_grad)
y = x.detach()
print(y.requires_grad)
print(x.eq(y).all())
```

输出结果：

```python
True
False
tensor(True)
```
