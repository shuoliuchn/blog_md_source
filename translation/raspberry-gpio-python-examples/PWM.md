# 使用 RPi.GPIO 中的 PWM

创建一个 PWM 实例对象：

```python
p = GPIO.PWM(channel, frequency)
```

启动 PWM：

```python
p.start(dc)   # dc 指代占空比 (0.0 <= dc <= 100.0)
```

更改频率：

```python
p.ChangeFrequency(freq)   # freq 指代新频率值，单位是 Hz
```

To change the duty cycle:

```python
p.ChangeDutyCycle(dc)  # 0.0 <= dc <= 100.0
```

停止 PWM：

```python
p.stop()
```

注意如果变量 `p` 超出范围，PWM 也将停止。

一个让 LED 每两秒闪烁一次的例子：

```python
import RPi.GPIO as GPIO
GPIO.setmode(GPIO.BOARD)
GPIO.setup(12, GPIO.OUT)

p = GPIO.PWM(12, 0.5)
p.start(1)
input('Press return to stop:')   # 在 Python 2 中使用 raw_input
p.stop()
GPIO.cleanup()
```

一个调亮/调暗 LED 的例子：

```python
import time
import RPi.GPIO as GPIO
GPIO.setmode(GPIO.BOARD)
GPIO.setup(12, GPIO.OUT)

p = GPIO.PWM(12, 50)  # channel=12 frequency=50Hz
p.start(0)
try:
    while 1:
        for dc in range(0, 101, 5):
            p.ChangeDutyCycle(dc)
            time.sleep(0.1)
        for dc in range(100, -1, -5):
            p.ChangeDutyCycle(dc)
            time.sleep(0.1)
except KeyboardInterrupt:
    pass
p.stop()
GPIO.cleanup()
```



## 译注：

占空比（Duty cycle）在电信领域中意思（引自 https://wenku.baidu.com/view/6540b82d453610661ed9f463.html）：

- 在一串理想的脉冲序列中（如方波），正脉冲的持续时间与脉冲总周期的比值
  - 例如：脉冲宽度1μs，信号周期4μS的脉冲序列占空比为0.25
- 在一段逶续工作时间内脉冲占用的时间与总时间的比值
- 负载周期在中文成语中有句话可以形容：「一天捕渔，三天晒网」则负载周期为0.25
- 占空比是高电平所占周期时间与整个周期时间的比值
- 占空比越大，高电平持续的时间越长，电路的开通时间就越长