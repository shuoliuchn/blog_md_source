# PRi.GPIO 的安装

如果你正在使用 Raspbian 你只需这样操作即可安装最新版本：

```shell
$ sudo apt-get update
$ sudo apt-get install python-rpi.gpio python3-rpi.gpio
```

如果你用的不是 Raspbian，还是建议你通过 pip 安装这个模块：
```shell
$  sudo pip install RPi.GPIO
```

如果你想从下载的安装包中安装你自己的版本，首先确定你安装了 Python 开发源！

对于 Raspbian：
```shell
$ sudo apt-get install python-dev python3-dev
```
安装模块：
```shell
$ sudo python setup.py install
  # or
$ sudo python3 setup.py install
```