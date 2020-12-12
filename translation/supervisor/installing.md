# supervisor 的安装

安装方式根据你要安装的系统中是否有网络连接而略有差异。

## 在有网络连接的系统中安装

### 使用 Pip 联网安装

Supervisor 可以通过 `pip install` 直接安装：

```bash
pip install supervisor
```

取决于你系统中 Python 的权限，你或许需要是 root 用户方能顺利使用 `pip`。

你也可以在虚拟环境中使用 `pip` 安装 supervisor。

### 联网的情况下，不使用 Pip 安装

如果你的系统中还没有安装 `pip`，你需要下载 Supervisor 发行版并手动安装。最新和以往的 Supervisor 发行版可以从 [PyPi](https://pypi.org/pypi/supervisor/) 下载。解压号软件的压缩包以后，执行 `python setup.py install` 命令。这需要有网络访问。这会下载并安装  Supervisor 的所有依赖，并最终安装 Supervisor 本身。

> 取决于你系统中 Python 的权限，你或许需要是 root 用户方能成功调用 `pip setup.py install`。

## 在无网络连接的系统中安装

如果你想要安装 Supervisor 的系统中无法访问互联网，你需要使用略有不同的方式安装。因为 `pip` 和 `python setup.py install` 都需要依赖网络访问才能下载依赖软件，这两种方法都直到依赖安装完成之后才能用。要想给一台没有网络连接的机器安装，从一个有网络连接的机器中获得如下依赖：

- 从 https://pypi.org/pypi/setuptools/ 获得（最新版本的）安装工具

把这些文件拷贝到可移动媒体中，并把它们放到目标机器上。分别按照每个依赖的说明将其按照到目标机器上。这通常就意味着解压每个文件，然后在解压的路径中调用 `python setup.py install`。最后，执行 supervisor 的 `python setup.py install`。

> 取决于你系统中 Python 的权限，你或许需要是 root 用户方能成功调用每一个安装包的 `pip setup.py install`。

## 安装发行包

一些 Linux 发行版提供了一种可通过系统包管理工具安装的 Supervisor 版本。这些包有第三方机构制作，俄铝出自 Supervisor 的开发者，而且常常包含一些有别于 Supervisor 发行版本的改动。

通过你的 Linux 发行版的包管理工具查看 supervisor 是否可获得。比如在 Ubuntu 系统，你可以运行 `apt-cache show supervisor`，而在 CentOS 系统你可以运行 `yum info supervisor`。

发行版的 Supervisor 包的一个特性是它们通常会集成到发行版的服务管理基础架构中，比如当系统启动时允许 `supervisord` 自动启动。

> 发行版的 Supervisor 包可能会一定程度上落后官方在 PyPI 发行的 Supervisor 包。比如 Ubuntu 12.04（发行于 2012 年 4 月）提供的是基于 Supervisor 3.0a8（发行于 2010 年 1 月）的包。

> 有用户反应说 Ubuntu 16.04 的 Supervisor 发行包和从前版本表现略有不同。在 Ubuntu 10.04、12.04 和 14.04 版本中，安装 supervisor 包的时候会配置系统在系统启动的时候开启 `supervisord`。而在 Ubuntu 16.04 中，初始化安装包的时候并不会作者简直。这个包后来把它修复了。参见 [Ubuntu Bug #1594740](https://bugs.launchpad.net/ubuntu/+source/supervisor/+bug/1594740) 获取更多信息。

## 创建配置文件

在安装好 Supervisor 后，执行 `echo_supervisord_conf`。这会在终端的输出中打印一个“简要”的 Supervisor配置文件。

如果你可以在终端中看到这个文件打印出来，再次执行 `echo_supervisord_conf > /etc/supervisord.conf` 命令。这个只有当你具有 root 权限是方才有效。

如果你没有 root 权限，或者你不想把 `supervisord.conf` 文件放到 `/etc/supervisord.conf` 里面，你可以把它放在当前路径（`echo_supervisord_conf > supervisord.conf`），不过要指定 `-c` 参数启动 **supervisord** 来说明配置文件的位置。

比如 `supervisord -c supervisord.conf`。其实在这种情况下使用 `-c` 参数是多余的，因为 **supervisord** 会首先在当前路径查找 `supervisord.conf` 文件，在这之后才会去其他位置查找这个文件，不过这样做也是能用的。参见 [*Running Supervisor*](http://supervisord.org/running.html#running) 获得关于 `-c` 参数的更多信息

一旦在你的文件系统中有了配置文件，你就可以开始随心所欲地修改它了。