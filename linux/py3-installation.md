## 编译安装 Python 3 并部署简单 django 项目

[TOC]

### 编译安装 Python 3

回忆一下我们使用 Windows 系统安装 Python 的过程：

1. 下载 exe 格式的 Python 安装包；
2. 双击这个 Python 安装包，设置安装路径；
3. 等待自动安装完毕，生成 Python 的可执行文件夹
4. 去 Python 的文件夹下，找到应用程序，执行我们的 Python 代码
5. 将 Python 执行文件所在的目录添加到环境变量中

在 Linux 系统下的编译安装过程，同在 Windows 下安装 Python 的道理是一致的。

1. 下载 Linux 版本的 Python 3 安装包（3.6.8 为 Python 版本，可按需选择）：

   ```shell
   wget  https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tgz
   ```

   若下载速度较慢，可以尝试着在 Windows 下使用迅雷等工具下载，然后通过 lrzsz 或 Xftp 等工具将安装包传到 Linux 系统中。

2. 解压缩源代码，准备编译安装：

   ```shell
   tar  -zxvf  Python-3.6.8.tgz
   ```

   参数解释：

   - -z 调用 gzip 指令，解压缩文件
   - -x 解包，解开 tar 的压缩格式
   - -v 显示操作过程
   - -f 指定压缩文件名字 

3. 在编译之前，需要提前安装好 Python 3 依赖的环境：

   ```bash
   yum install gcc patch libffi-devel python-devel  zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel -y
   # 或：
   yum -y install gcc make cmake zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel xz xz-devel libffi-devel
   ```

4. 进入 Python 3 源码目录，可以使用 pwd 命令确认，指定 Python 3 的安装路径：

   ```bash
   cd Python-3.6.8
   ./configure --prefix=/opt/python36/
   ```

   这里是指定 Python 3 的安装路径  ，`--prefix` 参数是指定安装到哪里，以及检查 Python 编译所需的依赖关系是否正常。

5. 指定安装路径完毕后，开始编译安装，就可以了，make 是编译，make install 是编译安装：

   ```bash
   make && make install    # 只有 make 编译成功了才会继续 make install 安装 Python 3
   ```

6. 编译安装结束后，配置 PATH 环境变量才能更方便的使用。

   要用就添加 Python 3 的安装目录到 PATH 环境变量中，需要设置 `/ect/profile` 文件：

   ```shell
   vim /etc/profile    # 打开全局环境变量配置文件
   ```

   在文件的最底部写入如下内容（注意 PATH 路径是有前后加载顺序的，写的位置可能会影响虚拟环境）：

   ```shell
   PATH="$PATH:/opt/python36/bin"
   ```

   其中 `/opt/python36/` 是第 4 步设置的 Python 安装位置。

   写好之后保存退出，然后重新登陆 ssh 终端，加载变量，此时检查变量是否正确：

   ```shell
   echo $PATH
   ```

   若变量中出现了 `/opt/python36/bin`，说明环境变量添加成功。

7. 验证 Python 3 和 pip 3 是否正常：

   ```shell
   which python3
   which pip3 
   ```

8. 升级 pip 3 版本的命令：

   ```shell
   pip3 install -i https://pypi.douban.com/simple --upgrade pip
   ```

### 署简单 django 项目

案例，创建 django 项目，运行一个 hello 页面，显示"来玩呀老弟，来学linux"。

1. 先安装 django 模块（使用豆瓣源）：

   ```shell
   pip3 install -i https://pypi.douban.com/simple django==1.11.9
   ```

2. 创建 django 项目 dj：

   ```shell
   django-admin startproject dj
   ```

3. 进入 dj 项目，创建应用 app01：

   ```shell
   cd dj
   
   django-admin startapp app01
       # 或
   python3 manage.py startapp app01
   ```

4. 编写 MTV：

   - 编辑 url 路由：

     ```shell
     vim dj/urls.py
     ```

   - 在其中写入路由：

     ```python
     from app01 import views
     urlpatterns = [
         url(r'^admin/', admin.site.urls),
         url(r'^hello/',views.hello),
     ]
     ```

   - 编辑 view 视图函数：

     ```shell
     vim app01/views.py
     ```

   - 在其中写入函数：

     ```python
     from django.shortcuts import HttpResponse
     def hello(requests):
         return HttpResponse("辛苦26期的帅哥美女们听课了，准备下课吃饭")
     ```

5. 修改 django 的配置文件，`settings.py`：

   ```shell
   vim settings.py
   ```

   找到 ALLOWED_HOSTS，将其修改为：

   ```python
   ALLOWED_HOSTS = ["*"]
   ```

6. 启动 django 服务，将其绑定在 0.0.0.0 上：

   ```shell
   python3 manage.py runserver  0.0.0.0:8888
   ```

7. 若无法访问服务，可尝试关闭防火墙：

   ```
   iptables -F
   ```

### 制作 Python 主程序和 pip 软连接

除了将 Python 的安装路径放到环境变量中意外，我们还可以将 Python 的主程序和 pip 建立软链接到环境变量中已经存在的目录中，同样能起到直接运行 Python 程序的任务：

```bash
ln -s /usr/local/python3/bin/python3.6 /usr/local/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3
```

需要注意的是，所有的路径都要写成绝对路径的形式。

运行上面的两条命令后，即可使用 Python 3 和 pip 了。