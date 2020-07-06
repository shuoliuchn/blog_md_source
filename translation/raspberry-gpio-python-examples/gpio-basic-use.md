# RPi.GPIO 模块基础

## 导入模块

导入 RPi.GPIO 模块：

```python
import RPi.GPIO as GPIO
```

这样做的好处是，你可以在后面的脚本中直接使用 GPIO 指代 RPI.GPIO 模块[^1]。

要想在导入模块的同时查看是否导入成功：

```python
try:
    import RPi.GPIO as GPIO
except RuntimeError:
    print("Error importing RPi.GPIO!  This is probably because you need superuser privileges.  You can achieve this by using 'sudo' to run your script")
```

## 针脚编号[^2]

在 `RPi.GPIO` 中，有两种对树莓派 IO 针脚的编号方式。第一种方式是使用 BOARD 编号系统。这种方法以树莓派电路板的 P1 头部开始计数。这种编号系统的好处是无论使用哪种版本的树莓派电路板，你的硬件总会正常工作。你不必重连线路或重写代码。

![GPIO-Pinout-Diagram-2](C:\Users\Sure\PyProject\神器\技术查阅手册\translation\raspberry-gpio-python-examples\basic.assets\GPIO-Pinout-Diagram-2-1579256892549-1579574055409.png)

第二种编号系统是 BCM 编号。这是更接近底层的工作——它指代的是 Broadcom SOC 的通道编号。这迫使你总要研究通道编号对应的树莓派电路板针脚的对照表。你的脚本可能会不兼容不同版本的树莓派电路板。

![raspberry-pi-pinout](C:\Users\Sure\PyProject\神器\技术查阅手册\translation\raspberry-gpio-python-examples\basic.assets\raspberry-pi-pinout-1579257214254-1579574064822.png)

声明你要用的编号系统（必须）：

```python
GPIO.setmode(GPIO.BOARD)
  # 或者
GPIO.setmode(GPIO.BCM)
```

要查看设置了哪一种针脚编号系统（比如，在另一个 Python 模块中）：

```python
mode = GPIO.getmode()
```

mode 将会是 `GPIO.BOARD`、`GPIO.BCM` 或 `None`。

## 警告

有可能你的树莓派的 GPIO 同时运行多个脚本/电路。这样做会导致一个结果，如果 `RPI.GPIO` 检测到某个针脚被配置成和默认模式（输入）不同的情况，当你尝试配置脚本时，可能会收到警告。禁用这些警告：

```python
GPIO.setwarnings(False)
```

## 配置一个通道

你需要把每一个正在使用的通道设置成输入或输出。把一个通道配置成输入：

```python
GPIO.setup(channel, GPIO.IN)
```

（`channel` 是基于你所声明的标号系统（BOARD 或 BCM）的通道编号）。

你可以从 [这里](输入.md) 找到更多关于设置输入通道的高级用法信息。

将一个通道配置成输出：

```python
GPIO.setup(channel, GPIO.OUT)
```

（`channel` 是基于你所声明的标号系统（BOARD 或 BCM）的通道编号）。

你也可以给你的输出通道指定一个初始值：

```python
GPIO.setup(channel, GPIO.OUT, initial=GPIO.HIGH)
```

## 配置多个通道

你可以一次性设置多个通道（0.5.8 及以后版本）。例如：

```python
chan_list = [11,12]    # 你想添加多少通道都成！
                       # 此外，你也可以用元组，例如：
                       #   chan_list = (11,12)
GPIO.setup(chan_list, GPIO.OUT)
```

## 输入

读取某个 GPIO 针脚的值：

```python
GPIO.input(channel)
```

（`channel` 是基于你所声明的标号系统（BOARD 或 BCM）的通道编号）。返回的结果将会是 `0` / `GPIO.LOW` / `False` 或者 `1` / `GPIO.HIGH` / `True`。

## 输出

设置某个 GPIO 针脚的输出状态：

```python
GPIO.output(channel, state)
```

（`channel` 是基于你所声明的标号系统（BOARD 或 BCM）的通道编号）。

State 可以是 `0` / `GPIO.LOW` / `False` 或者 `1` / `GPIO.HIGH` / `True`。

## 输出至多个通道

你可以一次性输出至多个通道（0.5.8 及以后版本）。例如：

```python
chan_list = [11,12]                             # 元组也成
GPIO.output(chan_list, GPIO.LOW)                # 全部设置成 GPIO.LOW
GPIO.output(chan_list, (GPIO.HIGH, GPIO.LOW))   # 把第一个设置成 HIGH 第二个设置成 LOW
```

## 清理

任何一个程序的末尾，清理掉任何一个你可能使用过的资源是一个好习惯。`RPi.GPIO` 也不例外。在不插拔连接线的情况下把你用完的通道复位为输入模式，可以避免因针脚短路而损坏你的树莓派。注意这只会清理掉你的脚本使用过的通道。注意 `GPIO.cleanup()` 也会清理掉使用中的针脚技术系统。

在你的脚本末尾进行清理操作：

```python
GPIO.cleanup()
```

有可能在你的程序退出时，并不希望清理掉所有通道，而是留一些保持配置工作状态。你可以清理单个通道，一个列表或元组的通道：

```python
GPIO.cleanup(channel)
GPIO.cleanup( (channel1, channel2) )
GPIO.cleanup( [channel1, channel2] )
```

## 树莓派电路板信息和 `RPi.GPIO` 版本

查看你的树莓派信息：

```python
GPIO.RPI_INFO
```

查看树莓派电路板的版本：

```python
GPIO.RPI_INFO['P1_REVISION']
GPIO.RPI_REVISION    # 不建议
```

查看 `RPi.GPIO` 的版本：

```python
GPIO.VERSION
```



## 译注

[^1]: 貌似使用 `from RPi import GPIO` 的形式导入也能达到同样的效果
[^2]: 更详细的接口信息，参见 https://pinout.xyz/