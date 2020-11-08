# mysqlclient 安装

新版本的 Django 都不支持使用很方便安装的 pymysql。可以通过修改源码的方式来实现对 pymysql 的支持。但是官方不建议的操作自然有它的道理，而且 pymysql 的效率的确不高。相比之下，C 语言开发的 mysqlclient 就很高效。当然，随之而来的是安装十分不便，无法直接 pip 安装，需要先安装依赖才行。

这里介绍几个常用的 Linux 发行版安装 mysqlclient 的方法。

### ArchLinux（或 Manjaro）

Arch（或 Manjaro）直接通过 pacman 安装即可：

```shell
pacman -S python-mysqlclient
```

### CentOS（红帽）

CentOS 有 Python、MySQL 的开发工具包，安装好以后，就可以使用 pip 安装 mysqlclient 了：

```shell
yum install mysql-devel
yum install python-devel
pip install mysqlclient
```

### Ubuntu

Ubuntu 也是安装一些 Mysql 的依赖

```shell
sudo apt-get install libmysqlclient-dev
sudo apt-get install python-dev    # python 3 要装 python3-dev
pip install mysqlclient
```

此时如果遇到如下错误

```shell
Collecting mysqlclient
  Using cached https://files.pythonhosted.org/packages/f4/f1/3bb6f64ca7a429729413e6556b7ba5976df06019a5245a43d36032f1061e/mysqlclient-1.4.2.post1.tar.gz
    Complete output from command python setup.py egg_info:
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
    ModuleNotFoundError: No module named 'setuptools'
    
    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-p8wpb1kf/mysqlclient/
```

需要安装一下 setuptools 包：

```bash
pip install setuptools==33.1.1
```

