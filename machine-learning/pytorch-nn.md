# 使用 Pytorch 构建神经网络

 [toc]

## 关于 torch.nn

使用 Pytorch 来构建神经网络，主要用到的工具都在 torch.nn 包中。nn 依赖于 autograd 来定义模型，并对其自动求导。

## 构建神经网络的典型流程

Pytorch 是一套成熟的框架，可以通过一个定式来构建神经网络：

1. 定义一个拥有可学习参数的神经网络
2. 遍历训练数据集
3. 处理输入数据使其流经神经网络
4. 计算损失值
5. 将网络参数的梯度进行反向传播
6. 以一定的规则更新网络的权重

## 定义神经网络

我们首先定义一个 Pytorch 实现的神经网络：

```python
# 导入若干工具包
import torch
from torch import nn
from torch.nn import functional as F


# 定义一个简单的网络类
class Net(nn.Module):

    def __init__(self):
        super(Net, self).__init__()
        # 定义第一层卷积神经网络，输入通道维度=1，输出通道维度=6，卷积核大小3*3
        self.conv1 = nn.Conv2d(1, 6, 3)
        # 定义第二层卷积神经网络，输入通道维度=6，输出通道维度=16，卷积核大小3*3
        self.conv2 = nn.Conv2d(6, 16, 3)
        # 定义三层全连接网络
        self.fc1 = nn.Linear(16 * 6 * 6, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        # 在(2, 2)的池化窗口下执行最大池化操作
        x = F.max_pool2d(F.relu(self.conv1(x)), (2, 2))
        x = F.max_pool2d(F.relu(self.conv2(x)), 2)
        x = x.view(-1, self.num_flat_features(x))
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

    def num_flat_features(self, x):
        # 计算size, 除了第0个维度上的batch_size
        size = x.size()[1:]
        num_features = 1
        for s in size:
            num_features *= s
        return num_features


net = Net()
print(net)
```

输出结果：

```python
Net(
  (conv1): Conv2d(1, 6, kernel_size=(3, 3), stride=(1, 1))
  (conv2): Conv2d(6, 16, kernel_size=(3, 3), stride=(1, 1))
  (fc1): Linear(in_features=576, out_features=120, bias=True)
  (fc2): Linear(in_features=120, out_features=84, bias=True)
  (fc3): Linear(in_features=84, out_features=10, bias=True)
)
```

可以通过 `net.parameters()` 获得模型中所有可训练参数：

```python
params = list(net.parameters())
print(len(params))
print(params[0].size())
```

输出结果：

```python
10
torch.Size([6, 1, 3, 3])
```

假设图像的输入尺寸为 32 * 32：

```python
input = torch.randn(1, 1, 32, 32)
out = net(input)
print(out)
```

输出结果：

```python
tensor([[ 0.0624, -0.1179, -0.0679, -0.1166,  0.0498, -0.1650, -0.0463,  0.0497,
         -0.1689, -0.0145]], grad_fn=<AddmmBackward>)
```

有了输出张量后，就可以执行梯度归零和反向传播的操作了：

```python
net.zero_grad()
out.backward(torch.randn(1, 10))
```

需要注意的是，torch.nn 构建的神经网络只支持 mini-batches 的输入，不支持单一样本的输入。比如，nn.Conv2d 需要一个4`D Tensor，形状为 (nSamples, nChannels, Height, Width)`。如果你的输入只有单一样本形式，则需要执行 `input.unsqueeze(0)`，主动将 3D Tensor 扩充成 4D Tensor。

## 损失函数

损失函数的输入是一个输入的 pair：`(output, target)`，然后计算出一个数值来评估 output 和 target 之间的差距大小。在 torch.nn 中有若干不同的损失函数可供使用，比如 nn.MSELoss 就是通过计算均方差损失来评估输入和目标值之间的差距。

应用 nn.MSELoss 计算损失的一个例子：

```python
output = net(input)
target = torch.randn(10)

# 改变 target 的形状为二维张量，为了和 output 匹配
target = target.view(1, -1)
criterion = nn.MSELoss()    # 损失函数，一般命名为 criterion

loss = criterion(output, target)
print(loss)
```

输出结果：

```python
tensor(1.0564, grad_fn=<MseLossBackward>)
```

关于方向传播的链条，如果我们跟踪 loss 反向传播的方向，使用 `.grad_fn` 属性打印，将可以看到一张完整的计算图如下：

```python
input -> conv2d -> relu -> maxpool2d -> conv2d -> relu -> maxpool2d
      -> view -> linear -> relu -> linear -> relu -> linear
      -> MSELoss
      -> loss
```

当调用 `loss.backward()` 时，整张计算图将对 loss 进行自动求导，所有属性 `requires_grad=True` 的 Tensors 都将参与梯度求导的运算，并将梯度累加到 Tensors 中的 `.grad` 属性中：

```python
print(loss.grad_fn)  # MSELoss
print(loss.grad_fn.next_functions[0][0])  # Linear
print(loss.grad_fn.next_functions[0][0].next_functions[0][0])  # ReLU
```

输出结果：

```python
<MseLossBackward object at 0x000002876D137B20>
<AddmmBackward object at 0x000002876D32A550>
<AccumulateGrad object at 0x000002876D35D490>
```

## 反向传播（backpropagation）

在 Pytorch 中执行反向传播非常简便，全部的操作就是 `loss.backward()`。

需要注意的是，在执行反向传播之前，要先将梯度清零，否则梯度会在不同的批次数据之间被累加。

如下是执行一个反向传播的小例子：

```python
# Pytorch 中执行梯度清零的代码
net.zero_grad()

print('反向传播前的 conv1.bias.grad')
print(net.conv1.bias.grad)

# Pytorch 中执行反向传播的代码
loss.backward()

print('反向传播后的 conv1.bias.grad')
print(net.conv1.bias.grad)
```

输出结果：

```python
反向传播前的 conv1.bias.grad
tensor([0., 0., 0., 0., 0., 0.])
反向传播后的 conv1.bias.grad
tensor([-0.0243, -0.0157,  0.0185,  0.0255,  0.0051, -0.0291])
```

## 更新网络参数

更新参数最简单的算法是 SGD（随机梯度下降），具体的算法公式表达式为：`weight = weight - learning_rate * gradient`。

首先用传统的 Python 代码来实现 SGD 如下：

```python
learning_rate = 0.01
for f in net.parameters():
    f.data.sub_(learning_rate * f.grad.data)
```

然后使用 Pytorch 官方推荐的标准代码如下：

```python
# 首先导入优化器的包，optim 中包含若干常用的优化算法，比如 SGD，Adam 等
from torch import optim

# 通过 optim 创建优化器对象
optimizer = optim.SGD(net.parameters(), lr=0.01)

# 将优化器执行梯度清零的操作
optimizer.zero_grad()

output = net(input)
loss = criterion(output, target)

# 对损失值执行反向传播的操作
loss.backward()
# 参数的更新通过一行标准代码来执行
optimizer.step()
```

