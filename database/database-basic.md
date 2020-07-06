## 数据库的基本概念和 Windows 下安装 MySQL

[TOC]

### 数据库的概念

我们从前都是把数据存放到文件当中。但是对于真正的生产环境而言，文件存储有其先天的不足。

比如，很多情况下，我们的客户端和服务器都不止一个。而如果把数据只以文件的形式存储到一个服务器上，另一台服务器可能不能及时接收到相应。对于用户信息之类的重要数据而言，这将是不可接受的。而且我们往往一台计算机只进行一种任务。这样的话，即便有一台计算机出了问题，其他计算机依然可以继续正常工作，整个系统的容灾性会很好。

于是，很多情况下，我们会把储存数据的任务交给独特的计算机，也就是数据库。

![未命名文件](database-basic.assets/databaserelationshipsingle.png)

当然，一台数据库也可能不能满足我们对多任务处理和数据安全的要求，我们还会使用多台计算机协调备份数据。

![数据库关系](database-basic.assets/databaserelationshipmultiple.png)

数据库之间实时要进行通信，一旦某一个数据库中的数据发生了改变，另一个数据库也要及时备份。

我们其实可以按照从前学过的 socket 网络编程和文件操作的只是来实现数据库的操作。但是，如果要保证能够完美地支持高并发，支持多台计算机完美协调，将会是一个极其困难繁琐的工作。

好在有很多很厉害的程序员已经帮我们写好了这类处理存储数据的工具，我们可以直接拿来用。这就是我们要学期的数据库管理系统。

**数据库是一个存储数据的工具**。

数据库帮我们解决了很多问题：

- 安全认证
- 并发问题，多个程序都请求同一个数据库获取数据
- 优化：缓存 cache，分析
- 容灾：数据库搭建集群

这里解释一下数据库对我们存储数据的优化。当数据量很大的时候，我们先想要从中获取想要的数据可能会比较麻烦。而有些数据，我们又需要经常地反复地获取。如果每次数据库都从硬盘中重新查找，将会耗费很多时间。于是，很多数据库都对这种情况做出了优化。当我们使用完一条数据之后，这条数据会储存到一个暂存区cache中。当我们下次还想获取同样的数据时，不会去硬盘中查找，而是先去缓存cache中查找。如果能够找到，那就直接使用。这样一来，对于一些经常使用的数据，会节省很多查找的时间，从而提高程序的运行效率。

### 数据库的基本术语

数据库经过这么多年的发展，已经相当成熟。程序员间对数据库中的一些概念有了一套约定俗成的称谓，也就是数据库的基本术语。我们需要了解这些数据，以便日后能够跟人更好地交流学习。

数据库管理系统（DBMS，database management system）：

- 管理着一个大的文件系统
- 包括文件夹（数据库）和文件（表）

数据库（DB，database）：就是文件夹

表（table）：文件

一条数据：文件中的每一行数据

数据：data

数据库管理员（DBA）：database administrator

需要指明的是，我们口头上常说的数据库，并不单单指文件夹。我们说的数据库，可能指文件夹，也可能指数据库管理系统，还可能指整个数据库的软硬件体系。具体指代哪一个，要因地制宜，不可钻牛角尖。

服务器和数据库服务器的比较：

- **服务器**就是部署了服务的机器
- **数据库服务器**指的是安装了数据库管理软件的计算机，可以提供服务

### 数据库的分类

数据库根据其数据间的逻辑，可分为关系型数据库和非关系型数据库两类，其代表性的数据库分别有：

- 关系型数据库：MySQL，Oracle，SQL server，SQLite
- 非关系型数据库：Redis，MongoDB，HBase，MemCache

### MySQL 的安装

我们要学习的数据库是 MySQL。选择这个数据库的原因是，它发展的时间较久，总体而言比较成熟。而且它开源免费，深受互联网公司欢迎。基本上，我们国家的互联网公司，使用的关系型数据库，90% 以上都是 MySQL。

最新的 MySQL 版本是 8.0 版本。但是一般而言，新版本的软件不确定性很多。比如兼容性和稳定性都不很确定。大多数时候，我们都会选择一个从前的稳定版本来使用。于是，我们安装的不是最新的 MySQL 8.0 版本，而是 5.6 版本。

之所以也不使用 5.7 版本，是因为 5.7 版本在初始化时会默认生成一个密码，如果没有记住又没来得及修改很麻烦，所以不推荐使用。

我们下载 MySQL 只能来自两个途径，[MySQL官方网站](www.mysql.com) 和公司给我们提供的 MySQL。其他途径下载的 MySQL 有可能会存在安全隐患，极度不推荐使用。数据是极其重要的，万不可以马虎。

首先，我们在浏览器中输入 `www.mysql.com` 进入 [MySQL官方网站](www.mysql.com)。点击主页上的 `DOWNLOADS` 标签，进入 [MySQL的下载页](https://www.mysql.com/downloads/)，`https://www.mysql.com/downloads/`。

![1572174764360](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572174764360.png)

在下载页中，找到 MySQL 社区版下载的入口，`MySQL Community (GPL) Downloads »`，进入到 [社区版下载页](https://dev.mysql.com/downloads/)，`https://dev.mysql.com/downloads/`。

![1572174991385](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572174991385.png)

找到需要下载在哪种平台使用的 MySQL。我用的是 Windows 系统，所以选择 `MySQL Installer for Windows`。进入到 [最新版本MySQL的下载页面](https://dev.mysql.com/downloads/installer/)，`https://dev.mysql.com/downloads/installer/`。如果是 macOS 或 Linux 系统，也可以按自己的需要下载不同的版本。

![1572175205080](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572175205080.png)

这个页面是最新的 8.0 版本的下载页面。按我们刚刚所说，我们要下载的是 5.6 版本。点击右上角的 `Looking for previous GA versions?`，进入到版本选择界面。

![1572175411998](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572175411998.png)

在下拉框中选择我们要下载的 5.6.45 版本。

这个方法虽然也能找到 5.6 版本 MySQL 的安装包，但只能找到 32 位版本。如果想要下载 64 位版本的安装包，可以去 [5.6版本专门的下载界面](https://dev.mysql.com/downloads/mysql/5.6.html#downloads)。

![1572175886823](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572175886823.png)

下载好了之后，将压缩包解压到文件夹中。最好把它解压到根目录中。

需要注意的是，安装路径要尽量避免出现中文。而且不可以有 `\n \t` 之类有特殊含义的字母。

使用 PyCharm 创建一个名为 `my.ini` 的文件，并在文件中写入下面的内容（千万不要用记事本打开，现在不要，以后也不要）：

```bash
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[mysqld]
#设置3306端口
port = 3306
# 设置mysql的安装目录
basedir=C:\mysql-5.6.46-winx64
# 设置mysql数据库的数据的存放目录
datadir=C:\mysql-5.6.46-winx64\data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

如果你是复制粘贴过去的，首先要检查每一行后面是否有空格。如果有，一定要删除干净。**每一行后面都不可以有任何空格**，否则可能会出现莫名其妙的错误。

然后，将 `basedir` 和 `datadir` 两个修改成你刚刚解压的 MySQL 文件所处的路径。

修改完成后，保存并关闭。然后把这个 `my.ini` 文件放到刚刚解压的 MySQL 根目录中。

![1572176306914](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572176306914.png)

数据会存放在 data 文件夹中，而命令的执行程序则被放在 bin 文件夹中。

![1572176488017](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572176488017.png)

我们需要把 bin 所在的目录放到环境变量中，这样才能顺利使用 MySQL 的各种命令。

![1572176567949](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572176567949.png)

以**管理员身份**运行终端 cmd，在终端中输入下面两条命令：

```bash
mysqld install
net start mysql
```

如果提示安装成功，则说明一切安装顺利。

![QQ图片20191025205910](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/QQ图片20191025205910.png)

![QQ图片20191025205913](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/QQ图片20191025205913.png)

如果输入第一条命令时，提示说 mysqld 不是系统命令，说明没有成功将 bin 目录添加到环境变量中，需要检查一下环境变量。

另外一个需要注意的是一定要以管理员身份运行 cmd。具体方法是，在搜索框（比如小娜）中，搜索 cmd，然后右键出现的 cmd，选择以管理员身份运行即可。

![1572177013828](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572177013828.png)

还有一点，不可以双击打开 `my.ini` 文件。因为 Windows 使用的是 gbk 编码，而我们需要它使用 utf-8 编码。但是，如果我们双击打开文件，有可能会改变文件的编码方式，从而导致错误。如果需要编辑，可以使用 PyCharm 或者 notepad++ 之类的能够指定编码的编辑器。

我们刚刚输入了两条命令，其含义为：

1. `mysqld install`，在命令行中启动 `mysqld.exe` 文件，传入了一个参数 install
2. `net start mysql`，告诉操作系统启动已经安装好的 MySQL 服务

![1572185570023](https://gitee.com/oldboy-python-full-stack-26/19083026021/raw/master/week08/day33/day33%20数据库初识.assets/1572185570023.png)

MySQL 的服务默认开机自启，只要我们不关闭，就会一直在后台运行。如果我们想要关闭这个服务，可以使用与开启服务类似的命令：

```bash
net stop mysql
```

只有安装 MySQL 和开启关闭 MySQL 服务时需要使用管理员身份运行 cmd，其他操作都可以在普通的 cmd 中运行。

另外，我们还需要区分 MySQL 的服务端和客户端：当我们执行 `mysql.exe` 文件时，实际上执行的是客户端。

