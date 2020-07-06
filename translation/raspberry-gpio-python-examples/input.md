# 输入

在你的程序中获取 GPIO 输入的方式有很多。第一种且最简单的方法是在一个特定的时间点检查输入值。这种方法被称作“轮询”，而且如果你的程序在错误的时间读取值的话，可能遗漏某个输入。轮询是回环往复执行的，可能计算密集。另一种响应 GPIO 输入的方式是使用“中断”（边缘检测）。边缘指的是一个从高到低（递减边缘）或从低到高（递增边缘）的转换。

## 上拉/下拉电阻[^1]

如果输入阵脚没有连接任何东西，它会“浮动”。换句话说，他的读数值是未定义的，因为它直到你按下按钮或开关才连接到电路中。它的结果可能变化幅度很大，因为收到的主要是噪音。

为了规避这些噪音，我们可以使用一个上拉或下拉电阻，设置输入的初始值。可是通过硬件和软件配置上拉/下拉电阻。在硬件上，在输入通道和 3.3V（上拉）或 0V（下拉）之间放一个 10K 的电阻是很常见的。RPi.GPIO 模块允许你使用软件配置 Broadcom SOC ：

```python
GPIO.setup(channel, GPIO.IN, pull_up_down=GPIO.PUD_UP)
  # 或者
GPIO.setup(channel, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)
```

（channel 指代的是基于你声明的编号系统对应的通道编号——BOARD 或 BCM）。

## 测试输入（轮询）

你可以在某个时刻拍一个输入快照：

```python
if GPIO.input(channel):
    print('Input was HIGH')
else:
    print('Input was LOW')
```

循环轮询等待按钮按下的动作：

```python
while GPIO.input(channel) == GPIO.LOW:
    time.sleep(0.01)  # 等待 10 ms 让 CPU 能做些别的事情
```

（这个例子假设按下按钮把输入从 LOW 变成 HIGH）

## 中断和边缘检测

边缘指的是电信号从低到高（递增边缘）或从高到低（递减边缘）的变化。我们常常更关心一个输入状态的变化而不是它的值的变化。这种状态的变化就是一个 *事件*。

为了避免当你的程序忙于其他事的时候而错过一次按下按钮的动作，有两种方式来规避它：

- `wait_for_edge()` 方法
- `event_detected()` 方法
- 当边缘被检测到时，会运行线程回调函数

### `wait_for_edge()` 方法

`wait_for_edge()` 方法设计成直到检测到边缘才会执行。换句话说，上面等待按钮按下的例子可以被这样重写：

```python
GPIO.wait_for_edge(channel, GPIO.RISING)
```

注意你可以检测的边缘类型有：`GPIO.RISING`、`GPIO.FALLING` 或 `GPIO.BOTH`。这种方式的好处是它不占用很多 CPU，于是给其他任务的资源就很多了。

如果你指向等待一个特定的时间段，你可以使用 `timeout` 参数：

```python
# 等待一个递增边缘至多 5 秒钟（timeout 的单位是毫秒）
channel = GPIO.wait_for_edge(channel, GPIO_RISING, timeout=5000)
if channel is None:
    print('Timeout occurred')
else:
    print('Edge detected on channel', channel)
```

### `event_detected()` 方法

`event_detected()` 方法被设计用来和其他操作一同在循环中使用。不过与轮询不同的是，即便当 CPU 正忙于做其他事情，也不会错过输入状态的改变。当使用诸如 Pygame 或 PyQt 这种有一个即时监听和响应 GUI 事件的主循环的程序中会很有用。

```python
GPIO.add_event_detect(channel, GPIO.RISING)  # 给一个通道添加递增边缘检测
do_something()
if GPIO.event_detected(channel):
    print('Button pressed')
```

注意你可以检测这些事件：`GPIO.RISING`、`GPIO.FALLING` 或者 `GPIO.BOTH`。

### 线程回调

`RPi.GPIO` 会为回调函数运行另一个线程。这意味着回调函数可以在你的主函数运行的同时运行，一旦触发边缘就会立即响应。例如：

```python
def my_callback(channel):
    print('This is a edge event callback function!')
    print('Edge detected on channel %s'%channel)
    print('This is run in a different thread to your main program')

GPIO.add_event_detect(channel, GPIO.RISING, callback=my_callback)  # 给通道增加递增边缘检测
...你程序的其他部分...
```

如果你需要多个回调函数：

```python
def my_callback_one(channel):
    print('Callback one')

def my_callback_two(channel):
    print('Callback two')

GPIO.add_event_detect(channel, GPIO.RISING)
GPIO.add_event_callback(channel, my_callback_one)
GPIO.add_event_callback(channel, my_callback_two)
```

注意在这种情况下，回调函数是顺序执行，而非并发执行。这是因为中有一个线程用于回调，在这个线程中，每个回调按照他们定义的顺序依此运行。

### 开关去抖

你可能已经发现了，每次按下按钮的时候，回调函数被调用了不止一次。这是所谓“开关抖动”的结果。有两种方法应对开关抖动：

- 在你的开关中加一个 0.1uF 的电容器
- 软件去抖
- 二者联用

通过软件去抖，在你声明回调函数时增加 `bouncetime=` 参数。抖动时间（bouncetime）的单位是毫秒。例如：

```python
# 给一个通道增加递增边缘检测，忽略 200ms 操控开关时产生的抖动
GPIO.add_event_detect(channel, GPIO.RISING, callback=my_callback, bouncetime=200)
```

或者

```python
GPIO.add_event_callback(channel, my_callback, bouncetime=200)
```

### 移除事件检测

如果因为某些原因，你的程序不再需要检测边缘时间，可以把它们终止：

```python
GPIO.remove_event_detect(channel)
```



## 译注

[^1]: **上拉电阻**：将一个不确定的信号（高或低电平），通过一个电阻与电源VCC相连，固定在高电平；**下拉电阻**：将一个不确定的信号（高或低电平），通过一个电阻与地GND相连，固定在低电平