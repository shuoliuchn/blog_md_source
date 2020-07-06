# 腾讯云环境配置

[TOC]

教程使用服务器：腾讯云 1核 2GB 1Mbps 基础入门版

操作系统：Centos 7



## 登录终端服务器

登陆终端服务器有很多种方式，这里只介绍两种比较常用的：腾讯云控制台直接登录和 SSH 登录。

### 腾讯云控制台登录终端服务器

首先，登录 [腾讯云控制台](https://console.cloud.tencent.com/cvm/instance/) 。购买云服务器我就不介绍了吧，花钱的事他们弄得都很简单且一目了然。

不出意外的话，在登陆进来之后，就能看见购买到的主机信息：

![1575444255165](tencent-build.assets\1575444255165.png)

当然，如果没有看见主机也没关系，很可能是因为机房地址没有选对。在左上角**实例**的旁边可以选择机房地址。比如，我的机房是在上海，就选择上海。你的机房在哪就选哪里。

![1575444846647](tencent-build.assets\1575444846647.png)

初次登陆，要给服务器设置密码，点击**更多** --> **密码/密钥** --> **重置密码**（也可能是**设置密码**之类的，反正跟密码有关就成）

![1575445171283](tencent-build.assets\1575445171283.png)

重置密码也是要验证身份的，微信扫一扫即可，十分方便（不便中的方便）。然后按照要求重置密码即可。

重置好密码，点击**登录**，输入刚刚设置的密码就可以登录到我们的云服务器中了：

![1575445896599](tencent-build.assets\1575445896599.png)

登录进来后是这样的：

![1575445938145](tencent-build.assets\1575445938145.png)

我的系统是 Centos 7，教程也主要是面向 Centos 7 做的。

### SSH 登录

除了使用腾讯云的网站控制台，我们也可以通过 SSH 更加灵活地操控我们的云服务器。

首先需要下载 Xshell。建议使用官网的[下载地址](https://www.netsarang.com/en/free-for-home-school/)下载。对于个人和家庭使用是免费的。因为实在外网，所以下载速度十分慢，请一定耐心等待。千万记得，这种涉及到根本安全的软件一定要在官网下载。不要图一时方便，给未来留下隐患。

另外，也建议同时把与 Xshell 同一个公司的 Xftp 下载，非常好用。

下载好点击下一步安装即可。

运行 Xshell，新建一个会话，主机那里写远程主机的 IP。

![1575446082251](tencent-build.assets\1575446082251.png)

然后点击右面的用户身份验证，输入用户名和密码：

![1575447201361](tencent-build.assets\1575447201361.png)

点击**键盘**，把 **DELETE** 和 **BACKSPACE** 键序列都设置成 **ASCII 127**。这样做是为了避免使用 Python 不能删除内容的麻烦：

![1575452402454](tencent-build.assets\1575452402454.png)

点击连接，即可连接到我们的服务器了：

![1575447282109](tencent-build.assets\1575447282109.png)

## 安装 Python 3

因为本教程主要面向小白，所以，下面所有的操作请一步一步跟随，不要落下，也尽量不要改步骤，除非你知道自己在做什么。

首先，到 `/usr/bin` 目录下查看系统安装的 Python 版本（一定要注意，是 **usr**，不是 **user**！）：

```bash
[root@VM_0_16_centos ~]# cd /usr/bin
[root@VM_0_16_centos bin]# ls python*
python  python2  python2.7
```

我的系统中只安装了 Python 2.7，需要再安装 Python 3 才能运行我的项目。

如果你的系统中安装了 Python 3，可以忽略这一部分，直接到下面的数据库安装。

在 `ls` 后面加上 `-al` 参数，我们可以看到这三个 Python 之间的联系：

```bash
python  python2  python2.7
[root@VM_0_16_centos bin]# ls -al python*
lrwxrwxrwx. 1 root root    7 Aug  8  2018 python -> python2
lrwxrwxrwx. 1 root root    9 Aug  8  2018 python2 -> python2.7
-rwxr-xr-x. 1 root root 7216 Apr 11  2018 python2.7
```

python 默认指向 python 2。这对我们并不友好，我们更希望让 python 指向 python 3。不过我们不要直接把它删除，而给这个 python 文件备份一下改个名字，以防日后遇到麻烦：

```bash
[root@VM_0_16_centos bin]# mv python python.bak
```

安装依赖：

```bash
[root@VM_0_16_centos bin]# yum -y install zlib-devel bzip2-devel openssl-devel openssl-static ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel lzma gcc

[root@VM_0_16_centos bin]# yum -y groupinstall "Development tools"
```

创建 `/usr/bin/python3` 目录：

```bash
[root@VM_0_16_centos bin]# mkdir /usr/local/python3
```

进入到刚刚创建的 python3 目录：

```bash
[root@VM_0_16_centos bin]# cd /usr/local/python3
[root@VM_0_16_centos python3]# ll
total 0
```

然后下载 Python 安装包。我下载的是 3.6.8 版本，是一个长期维护的稳定版本。你也可以自己选择需要下载的 Python 版本，只要把连接中的**两个** 3.6.8 换成你需要的版本号即可：

```bash
[root@VM_0_16_centos python3]# wget https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tgz
```

同样地，这是一个国外的网站，访问和下载的速度会比较慢，请耐心等待。

下载完成后，会在当前目录出现一个 `Python-3.6.8.tgz` 压缩包，使用命令解压即可。同样地，如果你下载的是其他版本，注意将这里的 3.6.8 改成你下载的 Python 版本号：

```bash
[root@VM_0_16_centos python3]# tar -xvf Python-3.6.8.tgz
```

解压完成后进入解压好的文件目录中：

```bash
[root@VM_0_16_centos python3]# cd Python-3.6.8
[root@VM_0_16_centos Python-3.6.8]#
```

确保我们是在上面的那个解压好的文件中，然后指定 Python 的安装目录：

```bash
[root@VM_0_16_centos Python-3.6.8]# ./configure --prefix=/usr/local/python3Dir
```

上面这行代码的意思是，把 Python 的安装目录指定一下，这样的话，里面的一些 bin 目录、lib 目录就都会存放在这个目录下面。如果不指定这个安装目录的话，最后 Python 的安装文件将分散到 linux 的默认目录。我们指定安装目录，以后卸载的话直接删除目录就可以干净卸载了。

确保我们所在的目录还是 /usr/bin/python3/Python-3.6.8，执行编译命令：

```bash
[root@VM_0_16_centos Python-3.6.8]# make
```

等待编译完成，开始安装：

```bash
[root@VM_0_16_centos Python-3.6.8]# make install
```

切换到我们刚刚指定的 Python 安装目录，可以看到里面已经有了文件：

```bash
[root@VM_0_16_centos Python-3.6.8]# cd /usr/local/python3Dir
[root@VM_0_16_centos python3Dir]# ll
total 16
drwxr-xr-x 2 root root 4096 Dec  4 17:14 bin
drwxr-xr-x 3 root root 4096 Dec  4 17:14 include
drwxr-xr-x 4 root root 4096 Dec  4 17:14 lib
drwxr-xr-x 3 root root 4096 Dec  4 17:14 share
[root@VM_0_16_centos python3Dir]# 
```

然后，我们切换到 `/usr/bin` 目录，给我们的 Python 3 建立一个名为 python 的软连接：

```bash
[root@VM_0_16_centos python3Dir]# cd /usr/bin
[root@VM_0_16_centos bin]# ln -s /usr/local/python3Dir/bin/python3 /usr/bin/python
```

不出意外的话，此时已经可以正常使用 Python 3 了。

但是注意，事情尚为结束！Centos 的 yum 依赖于 python 2，但是我们把 python 的指向改成了 python 3，会造成兼容性的问题，需要我们额外进行一些配置。

另外，下面的操作需要使用 vi 操作，像我一样不懂 vi 的同学，一定要看仔细了，要不然一定会被这东西搞晕的。

首先，确保你的输入法是英文输入法，然后输入命令：

```bash
[root@VM_0_16_centos bin]# vi /usr/bin/yum
```

在跳出的新界面中，不要乱按，听我的指示！

现在，按下键盘上的字母 `i` 键。如果一切都是按步骤操作的话，现在你应该已经可以编辑文件了。

此时，把文档的第一行 `#!/usr/bin/python` 改成 `#!/usr/bin/python2.7`。别的地方不要改。

然后，按下 `Esc` 键退出编辑。

接下来，键入冒号 `:`，即 `shift + ;`，进入命令操作，注意左下角会有输入框出现：

![1575451844433](tencent-build.assets\1575451844433.png)

输入 `wq` 后按下 `回车` 键，即可保存退出了。

除了 /usr/bin/yum 文件，我们还需要将 `/usr/libexec/urlgrabber-ext-down` 和 `/usr/bin/yum-config-manager` 两个文件的第一行修改为 python2.7，修改方法跟上面一样：

```bash
[root@VM_0_16_centos bin]# vi /usr/libexec/urlgrabber-ext-down
[root@VM_0_16_centos bin]# vi /usr/bin/yum-config-manager
```

检查更新，看看 yum 是否好用：

```bash
[root@VM_0_16_centos bin]# yum update
```

我们来查看一下链接情况：

```bash
[root@VM_0_16_centos bin]# ll -a python*
lrwxrwxrwx  1 root root   33 Dec  4 17:18 python -> /usr/local/python3Dir/bin/python3
lrwxrwxrwx. 1 root root    9 Aug  8  2018 python2 -> python2.7
-rwxr-xr-x. 1 root root 7216 Apr 11  2018 python2.7
lrwxrwxrwx. 1 root root    7 Aug  8  2018 python.bak -> python2
```

查看一下当前 Python 版本：

```bash
[root@VM_0_16_centos bin]# python -V
Python 3.6.8
```

我们还可以来一段 Hello World 代码测试一下：

```bash
[root@VM_0_16_centos bin]# python
Python 3.6.8 (default, Dec  4 2019, 17:12:30) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-36)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print('Hello World!')
Hello World!
>>> 
```

## 安装 MySQL 5.6

最新版本的 MySQL 是 8.0 版本，同样地，因为兼容性等问题，我们还是安装比较稳定经典的 5.6 版本。

首先，查看系统中是否已经安装了 MySQL：

```bash
[root@VM_0_16_centos ~]# rpm -qa|grep mysql
[root@VM_0_16_centos ~]# 
```

如果返回值为空，就说明没有安装 MySQL。

注意：新版本的 CentOS 7 中，默认的数据库是 MariaDB。使用 `yum install mysql` 命令只是更新 MariaDB 数据库，而不是安装 MySQL。

然后，看一下已经安装的 MariaDB 的版本：

```bash
[root@VM_0_16_centos ~]# rpm -qa|grep -i mariadb
mariadb-libs-5.5.64-1.el7.x86_64
[root@VM_0_16_centos ~]# 
```

确认我们有安装 MariaDB，现在就可以把 MariaDB 卸载：

```bash
[root@VM_0_16_centos ~]# rpm -qa|grep mariadb|xargs rpm -e --nodeps
[root@VM_0_16_centos ~]#
```

命令执行得很快，也没有任何提示。

我们可以通过之前的，查看 MariaDB 版本的方法，确认是否成功卸载：

```bash
[root@VM_0_16_centos ~]# rpm -qa|grep -i mariadb
[root@VM_0_16_centos ~]# 
```

创建一个 `/usr/local/mysql` 的路径，并进入改文件夹中，稍后我们会把 MySQL 的安装包下载到这个目录下：

```bash
[root@VM_0_16_centos ~]# mkdir /usr/local/mysql
[root@VM_0_16_centos ~]# cd /usr/local/mysql
[root@VM_0_16_centos mysql]# ll
total 0
```

下载 MySQL 5.6 的安装包：

```bash
[root@VM_0_16_centos mysql]# wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
```

安装 `mysql-community-release-el7-5.noarch.rpm` 包：

```bash
[root@VM_0_16_centos mysql]# rpm -ivh mysql-community-release-el7-5.noarch.rpm
```

安装完成后，会在 `/etc/yum.repos.d/` 目录下新增 `mysql-community.repo` 和 `mysql-community-source.repo` 两个 yum 源文件：

```bash
[root@VM_0_16_centos mysql]# cd /etc/yum.repos.d
[root@VM_0_16_centos yum.repos.d]# ll
total 44
-rw-r--r-- 1 root root  614 Dec  4 15:07 CentOS-Base.repo
-rw-r--r-- 1 root root 1309 Sep  5 21:05 CentOS-CR.repo
-rw-r--r-- 1 root root  649 Sep  5 21:05 CentOS-Debuginfo.repo
-rw-r--r-- 1 root root  230 Dec  4 15:07 CentOS-Epel.repo
-rw-r--r-- 1 root root  314 Sep  5 21:05 CentOS-fasttrack.repo
-rw-r--r-- 1 root root  630 Sep  5 21:05 CentOS-Media.repo
-rw-r--r-- 1 root root 1331 Sep  5 21:05 CentOS-Sources.repo
-rw-r--r-- 1 root root 6639 Sep  5 21:05 CentOS-Vault.repo
-rw-r--r-- 1 root root 1209 Jan 29  2014 mysql-community.repo
-rw-r--r-- 1 root root 1060 Jan 29  2014 mysql-community-source.repo
```

最后两个文件就是了。

查看可用的 mysql 安装文件：

```bash
[root@VM_0_16_centos yum.repos.d]# yum repolist all|grep mysql
mysql-connectors-community/x86_64 MySQL Connectors Community     enabled:    131
mysql-connectors-community-source MySQL Connectors Community - S disabled
mysql-tools-community/x86_64      MySQL Tools Community          enabled:    100
mysql-tools-community-source      MySQL Tools Community - Source disabled
mysql55-community/x86_64          MySQL 5.5 Community Server     disabled
mysql55-community-source          MySQL 5.5 Community Server - S disabled
mysql56-community/x86_64          MySQL 5.6 Community Server     enabled:    496
mysql56-community-source          MySQL 5.6 Community Server - S disabled
mysql57-community-dmr/x86_64      MySQL 5.7 Community Server Dev disabled
mysql57-community-dmr-source      MySQL 5.7 Community Server Dev disabled
```

接下来，就可以使用 yum 安装 MySQL 了：

```bash
[root@VM_0_16_centos yum.repos.d]# yum install mysql-server
```

问是否 OK 的话，选 y 即可。

同样的问题再次出现，MySQL 也是外网，需要给他点时间下载。

下载完成后，检查 MySQL 是否安装成功：

```bash
[root@VM_0_16_centos yum.repos.d]# rpm -qa|grep mysql
mysql-community-client-5.6.46-2.el7.x86_64
mysql-community-server-5.6.46-2.el7.x86_64
mysql-community-common-5.6.46-2.el7.x86_64
mysql-community-libs-5.6.46-2.el7.x86_64
mysql-community-release-el7-5.noarch
[root@VM_0_16_centos yum.repos.d]# 
```

MySQL 5.6.46 版本已经成功安装。

启动和关闭 MySQL 服务相关的命令：

```bash
systemctl start mysqld.service #启动 mysql
systemctl restart mysqld.service #重启 mysql
systemctl stop mysqld.service #停止 mysql
systemctl enable mysqld.service #设置 mysql 开机启动
```

此刻，已经完成了 MySQL 的安装。

接下来就是配置 MySQL 数据库：

```bash
[root@VM_0_16_centos etc]# vi /etc/my.cnf
```

参考配置：

```bash
[mysqld]
character-set-server=utf8
default-storage-engine=INNODB
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
symbolic-links=0
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

[client]
default-character-set=utf8
```

## 环境配置

### 安装PIP

下载 PIP 安装包：

```bash
[root@VM_0_16_centos etc]# mkdir /usr/local/pip
[root@VM_0_16_centos etc]# cd /usr/local/pip
[root@VM_0_16_centos pip]# wget https://bootstrap.pypa.io/get-pip.py
```

安装 PIP：

```bash
[root@VM_0_16_centos pip]# python get-pip.py
```

Django 和其他 Python 第三方包就可以使用 pip 直接安装了。

```bash
[root@VM_0_16_centos pip]# python -m pip install pymysql
```

### pip 国内源

这里是一些国内的 pip 镜像：

```http
阿里云 https://mirrors.aliyun.com/pypi/simple/
中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
豆瓣(douban) http://pypi.douban.com/simple/
中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/
华中理工大学：http://pypi.hustunique.com/
山东理工大学：http://pypi.sdutlinux.org/
```

使用国内源可以避免因服务器在国外而导致下载速度过慢的问题：

```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pyspider
```

### 安装依赖库

把我们需要的依赖库都找到是很麻烦的，不小心忘记某个库还会报错。

好在 pip 有 freeze 方法，我们可以直接把本地的环境导出到文件中：

```bash
pip freeze > requirements.txt
```

然后，将 requirements.txt 文件上传到云端，就可以批量安装我们需要的依赖库了：

```bash
pip install -r requirements.txt
```

### 部署 Django 应用

首先要同步数据库，然后尝试着运行 Django 项目：

```bash
sudo python3 manage.py runserver 0.0.0.0:80
```

在后台运行 django 应用，不随腾讯云 web 会话断开而关闭应用：

```bash
sudo nohup python3 manage.py runserver 0.0.0.0:80 &
```

- nohup（no hang up）用途：不挂断地运行命令

- &用途：在后台运行

若想查看后台 Django 进程的运行情况，可以使用命令：

```bash
ps -ef|grep "python"
```

输出结果中，就包含我们刚刚输入的命令：

```bash
root     12374  8836  0 17:58 pts/0    00:00:00 sudo nohup python3 manage.py runserver 0.0.0.0:80
root     12375 12374  0 17:58 pts/0    00:00:00 python3 manage.py runserver 0.0.0.0:80
root     12378 12375  1 17:58 pts/0    00:00:00 /usr/bin/python3 manage.py runserver 0.0.0.0:80
```

若要杀死 Django 进程，可以使用命令：

```bash
kill -s 9 12378
```

其中，12378 是最后一个 Django 命令的进程号，也就是真正运行中的进程。

对于后台运行的进程最好自定义进程名，省的找不到，可以在 manage.py 文件中加上进程名定义：

```python
try:
    import setproctitle
    setproctitle.setproctitle("xxxx")
except:
    pass
```

然后就可以通过命令过滤出对应的后台进程了：

```bash
ps -A | grep "xxxx"
```

###  部署数据库

安装 MySQL:

```bash
sudo apt install mysql-server
```

数据库的启动/关闭/重启：

```bash
sudo service mysql start
sudo service mysql stop
sudo service mysql restart
```

数据库密码修改：

```mysql
set password = password('123');
```

数据库备份：

```bash
sudo mysqldump -u username -p --databases dbname2 dbname2 > Backup.sql
```

数据库还原：

```bash
sudo mysqlhotcopy [option] dbname1 dbname2 backupDir
```

设置数据库的登陆方式为密码认证：

```mysql
USE mysql;
UPDATE user SET plugin='mysql_native_password' WHERE user='root';
FLUSH PRIVILEGES;
exit;
```

## 部署 Java 环境

### 安装 Java

首先，查看是否已经安装了 Java：

```shell
java -version
```

从结果上看，并没有：

```bash
Command 'java' not found, but can be installed with:

sudo apt install default-jre            
sudo apt install openjdk-11-jre-headless
sudo apt install openjdk-8-jre-headless 
```

不过他也给了我们提示命令。这些命令都是安装 Java jre，也就是运行环境的，可以应付我们绝大多数的使用环境。如果没有特殊需求，可以直接安装默认的 default-jre。为了确保兼容性，也可以安装 openjdk-8：

```shell
sudo apt install openjdk-8-jre
```

我们也可以安装 Java 8 jdk，也就是开发套件，期间需要输入 y 确认安装：

```shell
sudo apt install openjdk-8-jdk
```

输入命令 java -version 即可查看当前 Java 版本号：

许多 Java 写的程序使用 JAVA_HOME 变量来确定 Java 的安装位置，所以我们需要在环境变量中设置 JAVA_HOME 变量。

首先，使用命令确定 Java 的安装位置：

```shell
sudo update-alternatives --config java
```

从结果来看，我的 Java 安装在了 `/usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java`：

```shell
There is only one alternative in link group java (providing /usr/bin/java): /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
Nothing to configure.
```

打开 `/etc/environment` 环境变量文件：

```shell
sudo nano /etc/environment
```

在文件的末尾添加 JAVA_HOME 参数：

```shell
JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java"
```

对于 nano 编辑器来说，`ctrl + o` 保存，`ctrl + x` 退出。

重新加载此文件：

```shell
source /etc/environment
```

验证是否成功添加 JAVA_HOME 至环境变量：

```shell
echo $JAVA_HOME
```

响应数据确实是我们 Java 所在的路径，安装成功：

```shell
/usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
```

### 安装 `Node.js`

输入下面的命令可直接安装 `node.js`：

```bash
curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash -
sudo apt-get install -y nodejs
```

查看 Java、node 和 npm 版本已确认安装成功：

```shell
ubuntu@VM-0-16-ubuntu:~$ java -version
openjdk version "1.8.0_232"
OpenJDK Runtime Environment (build 1.8.0_232-8u232-b09-0ubuntu1~18.04.1-b09)
OpenJDK 64-Bit Server VM (build 25.232-b09, mixed mode)
ubuntu@VM-0-16-ubuntu:~$ node -v
v12.14.1
ubuntu@VM-0-16-ubuntu:~$ npm -version
6.13.4
```





## 参考网站

1. [Centos 7安装python3](https://www.cnblogs.com/simuhunluo/p/7704765.html)
2. [Centos中Python升级为3.X时yum出现except OSError, e: ^ SyntaxError: invalid syntax问题](https://blog.csdn.net/chaojiwanwan/article/details/71439066)
3. [Vim命令合集](https://www.cnblogs.com/softwaretesting/archive/2011/07/12/2104435.html)
4. [yum except KeyboardInterrupt, e: 错误](https://blog.csdn.net/Harith/article/details/17691137)
5. [CentOS7下使用YUM安装MySQL5.6](https://blog.csdn.net/pengjunlee/article/details/81212250)
6. [让python pip使用国内镜像](https://www.cnblogs.com/wqpkita/p/7248525.html)
7. [腾讯云快速完成python3.6开发环境搭建与django应用部署](https://www.cnblogs.com/LanTianYou/p/9482362.html)
8. [Django项目后台不挂断运行](https://blog.csdn.net/Rnger/article/details/79907884)
9. [django&python&linux后台运行](https://blog.csdn.net/woshizoe/article/details/24991589)
10. [MySQL 数据备份与还原](https://www.cnblogs.com/kissdodog/p/4174421.html)
11. [ERROR 1698 (28000): Access denied for user 'root'@'localhost'](https://www.cnblogs.com/feiffy/p/9825122.html)
12. [如何在Ubuntu 18.04上安装Java](https://cloud.tencent.com/developer/article/1162527)
13. [ubuntu安装nodejs](https://www.jianshu.com/p/c8929bea5fba)

