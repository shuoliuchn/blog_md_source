## pip 更换为国内源

有时候，使用 pip 安装 Python 第三方包会很慢。这是因为官方 pip 服务器设置在国外，有很高的网络延迟。

为了解决这个状况，有些大的公司或高校建立起自己的 pip 镜像源。我们从这些国内的镜像源下载第三方包，速度会快很多。

### 单次修改下载源

单次修改，只需在安装命令后面加上 `-i` 参数并指定下载源即可，例如：

```bash
pip3 install django==2.2.0 -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### 永久修改下载源

Linux 下，修改 `~/.pip/pip.conf `

注意：如果没有就创建一个文件夹及文件。在 Linux 中，文件夹以 `.` 开头表示是隐藏文件夹。

```
mkdir ~/.pip
sudo vim ~/.pip/pip.conf
```

在 `pip.conf` 中填写内容，如下：

```ini
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com
```

Windows 下，直接在用户目录中创建一个 pip 目录，如：`C:\Users\用户名\pip`，新建文件 `pip.ini`，内容同上。

这样，以后每次使用 pip 安装的时候，默认会使用国内的镜像源了。

### 常用国内镜像源

```
阿里云               https://mirrors.aliyun.com/pypi/simple/
中国科技大学          https://pypi.mirrors.ustc.edu.cn/simple/
豆瓣（douban）       https://pypi.douban.com/simple/
清华大学             https://pypi.tuna.tsinghua.edu.cn/simple/
```

建议使用清华或者阿里的源，豆瓣源我曾经遇到过更新不及时的情况。