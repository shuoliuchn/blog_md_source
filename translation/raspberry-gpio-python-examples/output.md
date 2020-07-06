# GPIO 输出

1. 首先配置 RPi.GPIO （参照 [这里](基础使用.md)）

```python
import RPi.GPIO as GPIO
GPIO.setmode(GPIO.BOARD)
GPIO.setup(12, GPIO.OUT)
```

2. 设置某个输出为高电平：

```python
GPIO.output(12, GPIO.HIGH)
 # 或
GPIO.output(12, 1)
 # 或
GPIO.output(12, True)
```

3. 设置某个输出为低电平：

```python
GPIO.output(12, GPIO.LOW)
 # 或
GPIO.output(12, 0)
 # 或
GPIO.output(12, False)
```

4. 同时输出至多个通道：

```python
chan_list = (11,12)
GPIO.output(chan_list, GPIO.LOW) # 全部设置成低电平
GPIO.output(chan_list, (GPIO.HIGH,GPIO.LOW))  # 第一个为低电平，第二个为高电平
```

5. 在你的程序末尾进行清理：

```python
GPIO.cleanup()
```

注意你可以将通过 `input()` 函数读取到的当前状态作为通道的输出值。例如切换输出：

```python
GPIO.output(12, not GPIO.input(12))
```