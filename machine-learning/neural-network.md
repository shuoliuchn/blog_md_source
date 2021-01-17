# 神经网络范式

这个神经网络范式是根据人民邮电出版社出版的哈里克·拉希德的著作《Python 神经网络编程》给的代码照抄过来的。原著代码在 [GitHub](https://github.com/makeyourownneuralnetwork/makeyourownneuralnetwork) 上开源，更多功能和数据可以去那里找。

神经网络是一个十分神奇的机器学习方法，它通过再简单不过的原理（线性关系）就可以实现各种神奇的学习操作，尽显简洁之美。

代码中的 main 中是一个识别手写数字的例子，这个神经网络能做的倒也不局限于此。这是一个完整的神经网络范式，能够胜任任何任务。

当然，这个代码更多的还是用来了解神经网络的原理。毕竟它是纯 Python 开发，且没有考虑并发的问题，效率上是比较捉襟见肘的。而且神经网络是一个成熟的机器学习模式，有很多高效的封装，没必要重复造轮子。

```python
import numpy as np
import scipy.special

# 定义神经网络类
class NeuralNetwork:
    # 初始化神经网络
    def __init__(self, input_nodes, hidden_nodes, output_nodes, learning_rate):
        # 定义输入层、隐藏层和输出层节点的数量
        self.inodes = input_nodes
        self.hnodes = hidden_nodes
        self.onodes = output_nodes

        # 学习率
        self.lr = learning_rate

        # 初始化权重矩阵
        # self.wih = np.random.rand(self.hnodes, self.inodes) - 0.5
        # self.who = np.random.rand(self.onodes, self.hnodes) - 0.5
        # 正态概率分布初始化权重矩阵
        self.wih = np.random.normal(0.0, pow(self.hnodes, -0.5), (self.hnodes, self.inodes))
        self.who = np.random.normal(0.0, pow(self.onodes, -0.5), (self.onodes, self.hnodes))

        # S 抑制函数
        self.activation_function = lambda x: scipy.special.expit(x)

    # 训练神经网络
    def train(self, input_list, target_list):
        inputs = np.array(input_list, ndmin=2).T
        targets = np.array(target_list, ndmin=2).T

        # 计算传到隐藏层的信号
        hidden_inputs = np.dot(self.wih, inputs)
        # 计算最终到达隐藏层的信号
        hidden_outputs = self.activation_function(hidden_inputs)

        # 计算到达输出层的信号
        final_inputs = np.dot(self.who, hidden_outputs)
        # 计算输出层输出的信号
        final_outputs = self.activation_function(final_inputs)

        # 误差 = 目标值 - 真实值
        output_errors = targets - final_outputs
        # 隐藏层的误差为输出误差根据权重拆分重组后的结果
        hidden_errors = np.dot(self.who.T, output_errors)

        # 更新权重矩阵
        self.who += self.lr * np.dot(output_errors * final_outputs * (1.0 - final_outputs), np.transpose(hidden_outputs))
        self.wih += self.lr * np.dot(hidden_errors * hidden_outputs * (1.0 - hidden_outputs), np.transpose(inputs))

    # 查询神经网络
    def query(self, input_list):
        # 将数据列表转换成二维数组
        inputs = np.array(input_list, ndmin=2).T

        # 计算传到隐藏层的信号
        hidden_inputs = np.dot(self.wih, inputs)
        # 计算最终到达隐藏层的信号
        hidden_outputs = self.activation_function(hidden_inputs)

        # 计算到达输出层的信号
        final_inputs = np.dot(self.who, hidden_outputs)
        # 计算输出层输出的信号
        final_outputs = self.activation_function(final_inputs)

        return final_outputs


if __name__ == '__main__':
    # 指定输入、隐藏和输出节点的数目
    input_nodes = 784
    hidden_nodes = 100
    output_nodes = 10

    # 指定学习率
    learning_rate = 0.2

    # 创建神经网络实例
    nn = NeuralNetwork(input_nodes, hidden_nodes, output_nodes, learning_rate)

    # 加载 mnist 训练数据
    training_data_file = open('mnist_train.csv', 'r')
    training_data_list = training_data_file.readlines()
    training_data_file.close()

    # 训练神经网络
    # 训练次代数目，即统一组数据训练几次
    epochs = 5
    for e in range(epochs):
        # 遍历所有训练数据集中的记录
        for record in training_data_list:
            # 数据之间以逗号分隔
            all_values = record.split(',')
            # 缩放转换输入数据范围至 0.01 - 1.0
            inputs = np.asfarray(all_values[1:]) / 255 * 0.99 + 0.01

            # 创建输出数据，正确值为 0.99，其他为 0.01
            targets = np.zeros(output_nodes) + 0.01
            # csv 每条数据的第一个值为期望值
            targets[int(all_values[0])] = 0.99
            nn.train(inputs, targets)

    # 测试神经网络
    # 加载训练数据
    test_data_file = open('mnist_test.csv', 'r')
    test_data_list = test_data_file.readlines()
    test_data_file.close()
    # 得分记录表，用来评价神经网络的表现情况，初始为空列表
    scorecard = []
    for record in test_data_list:
        all_values = record.split(',')
        # 第一条数据为正确答案
        correct_label = int(all_values[0])
        # print(correct_label, 'correct label')
        # 缩放转换输入数据
        inputs = np.asfarray(all_values[1:]) / 255 * 0.99 + 0.01
        # 查询神经网络
        outputs = nn.query(inputs)
        # outputs 中值最高的数据的索引
        label = np.argmax(outputs)
        # print(label, 'network\'s answer')
        # 将结果存放到得分记录表中，预测正确存为 1，错误存为 0
        scorecard.append(1 if label == correct_label else 0)
    # print(scorecard)
    scorecard_array = np.array(scorecard)
    print('performance =', scorecard_array.sum() / scorecard_array.size)
```

