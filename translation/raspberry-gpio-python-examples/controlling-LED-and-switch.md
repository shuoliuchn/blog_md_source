# GPIO 控制 LED 和开关

使用  [GPIO Zero](https://gpiozero.readthedocs.io/)  库，简单快速入门使用 Python 控制 GPIO 设备。

## LED

控制连接在 GPIO17 上的 LED，你可以使用这些代码：

```python
from gpiozero import LED
from time import sleep

led = LED(17)

while True:
    led.on()
    sleep(1)
    led.off()
    sleep(1)
```

在诸如 Thonny 之类的 IDE 中执行代码，就可以看见 LED 时亮时灭反复闪烁。

LED 方法包括 `on()`、`off()`、`toggle()` 和 `blink()`。

## 按钮

读取连接到 GPIO2 上的按钮的状态，你可以使用这些代码：

```python
from gpiozero import Button
from time import sleep

button = Button(2)

while True:
    if button.is_pressed:
        print("Pressed")
    else:
        print("Released")
    sleep(1)
```

从功能上讲有 `is_pressed`和 `is_held` 属性；回调 `when_pressed`、`when_released` 和 `when_held`；方法 `wait_for_press()` 和 `wait_for_release`。

## 按钮 + LED

把 LED 和 按钮联用，你可以使用这些代码：

```python
from gpiozero import LED, Button

led = LED(17)
button = Button(2)

while True:
    if button.is_pressed:
        led.on()
    else:
        led.off()
```

也可以这样：

```python
from gpiozero import LED, Button

led = LED(17)
button = Button(2)

while True:
    button.wait_for_press()
    led.on()
    button.wait_for_release()
    led.off()
```

或者：

```python
from gpiozero import LED, Button

led = LED(17)
button = Button(2)

button.when_pressed = led.on
button.when_released = led.off
```

## GPIO Zero 文档

GPIO Zero 支持很多其他的 GPIO 设备。在 [gpiozero.readthedocs.io](https://gpiozero.readthedocs.io/) 可以找到这个库的完整文档。