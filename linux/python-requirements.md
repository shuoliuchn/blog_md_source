## 导出导入 Python 依赖



写好 Python 项目，把它转移到线上却发现，服务器的 Linux 系统中，很多开发环境中 Python 的第三方包都没有安装。如果一个一个手动安装非常麻烦。好在 pip 支持依赖包信息的导出和导入，让这一切都变得简单起来。

只需三步，即可实现本地开发环境和线上环境的一致性：

1. 导出开发环境的解释器下所有的模块：

   ```shell
   pip3 freeze > requirements.txt
   ```

2. 发送这个 `requirements.txt` 文件给 Linux 机器，里面包含项目所有运行需要的模块了。

   如果开发环境是 macOS 或 Linux 系统，可以使用 scp 命令传输：

   ```shell
   scp requirements.txt root@192.168.248.128:/tmp/
   ```

   如果开发环境是 Windows 系统，可以使用其他工具，比如 lrzsz 或 xftp。甚至可以将文件中的内容直接复制粘贴到 Linux 中。

3. 在 Linux 机器上，使用命令读取这个文件，即可自动安装所有模块。pip 会自动读取这个文件每一行数据，自动进行模块安装：

   ```shell
   pip3 install -i https://pypi.douban.com/simple   -r requirements.txt
   ```

此时，只要安装正确，你的解释器模块环境就和开发环境的模块就一模一样了！