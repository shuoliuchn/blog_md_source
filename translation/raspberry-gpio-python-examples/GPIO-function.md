# gpio_function（通道）

显示指定 GPIO 通道的功能。
例如：

```python
import RPi.GPIO as GPIO

GPIO.setmode(GPIO.BOARD)
func = GPIO.gpio_function(pin)
```

将会从下面的功能中返回一个值：
`GPIO.IN`，`GPIO.OUT`，`GPIO.SPI`，`GPIO.I2C`，`GPIO.HARD_PWM`，`GPIO.SERIAL`，`GPIO.UNKNOWN`