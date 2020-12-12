## Linux 软件安装流程 - nginx 的安装

[TOC]

### Linux 软件的安装方式

Linux 安装软件有如下几种方式：

1. 源代码编译安装（最好的安装形式，可以自由定义安装路径，第三方功能扩展，以及获取官网最新的代码进行编译安装，缺点是对新手不友好）
2. yum 工具（新手最好的工具，自动化解决程序安装所需的依赖关系，自动下载且安装依赖，要求得配置 yum 仓库源，软件版本可能较低）
3. rpm 软件包手动安装（弃用，需要手动解决依赖关系，贼难受，不用）

本文以 nginx 为例，总结前两种软件的安装方式。

这两种安装方式的优缺点为：

1. 用 yum 安装，简单方便，但是难以使用第三方的功能扩展
2. 源代码编译安装，可以在编译的时候，扩展第三方功能

### yum 安装流程

1. 在一台新的 Linux 机器上，想要使用 yum 安装，必须得配置好 yum 仓库

2. 下载阿里的 yum 源，以及 epel 源

3. 下载好新的 yum 源之后，清空旧的 yum 缓存

   ```
   yum clean all
   ```

4. 最好生成新的 yum 缓存，缓存能够加速下载

   ```
   yum makecache
   ```

5. 安装 nginx：

   ```shell
   yum install nginx -y
   ```

若要卸载 yum 安装的 nginx，直接使用命令：

```shell
yum remove nginx -y    # 卸载nginx，自动处理依赖关系
```

### 编译安装流程

源代码编译安装步骤，编译步骤

- 首先确保安装了各种依赖，非必须

- 指定安装路径   `./configure --prefix=绝对路径`
- 开始编译  输入 make 指令
- 编译安装，生成可执行文件 make install  

以 nginx 为例，具体流程为：

1. 下载 nginx 源码，访问官方网站 http://tengine.taobao.org 找到源代码下载链接

   ```shell
   wget http://tengine.taobao.org/download/tengine-2.3.2.tar.gz
   ```

2. 解压源代码

   ```shell
   tar -zxvf tengine-w.3.2.tar.gz
   ```

3. 进入ngnix源码目录 指定安装路径

   ```shell
   cd tengine-w.3.2
   ./configure --prefix=/opt/tengine23
   ```

4. 开始编译安装

   ```shell
   make && make install
   ```

### 配置 PATH，添加 nginx 的执行文件

通过编译安装的 nginx，需要将 nginx 的执行文件所在目录添加到环境变量 PATH 中，基本流程如下：

1. 修改 `/etc/profile`，修改 PATH 的值：

   ```shell
   vim /etc/profile
   ```

   在文件最底部添加如下代码：

   ```shell
   PATH="$PATH:/opt/tengine23/sbin"
   ```

2. 手动读取 `/etc/profile` 文件：

   ```shell
   source /etc/profile
   ```

3. 此时 PATH 新变量已经生效：

   ```shell
   which nginx
   ```

4. nginx 可执行命令的常用方式：

   ```shell
   nginx -s reload    # 平滑重启，重新读取nginx配置文件，而不重启进程
   nginx    # 直接输入nginx，是代表启动，只能首次使用
   nginx -s stop    # 杀死nginx进程
   nginx -t    # 检测nginx.conf是否编写正确
   ```

