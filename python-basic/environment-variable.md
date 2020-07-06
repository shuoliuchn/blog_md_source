## Windows 系统环境变量的配置

Windows 系统中的可执行程序一般以 `exe` 做后缀。

把 exe 文件所在的文件夹放在系统环境变量的 path 变量中，就可以使用终端直接输入程序名执行该程序了。

例如，如果没有将 Python 所在目录放到环境变量中，在终端直接输入 python 命令就会提示 `‘python’ 不是内部或外部命令，也不是可运行的程序`：

<img src = "environment-variable.assets\py_setup_12.png">

要解决这个问题，可以将 Python 的安装目录放到环境变量中。

右键 `此电脑` （也可能叫 `我的电脑`、`计算机`、`此电脑` 等等）图标，点击 `属性`。点击左侧的`高级系统设置`。

<img src = "environment-variable.assets\py_setup_14.png">

也可以使用小娜或者其他工具搜索 `高级系统设置`，直接打开。

![1579578151110](environment-variable.assets\1579578151110.png)

选择 `环境变量`

<img src = "environment-variable.assets\py_setup_15.png">

在 `系统环境变量` 中找到 `Path` 变量，点击 `编辑`。

<img src = "environment-variable.assets\py_setup_16.png">

点击 `新建`。

<img src = "environment-variable.assets\py_setup_17.png">

将 Python 的安装路径插入到 `环境变量` 中，点击 `确定`，退出编辑界面。

<img src = "environment-variable.assets\py_setup_18.png">

然后就可以在终端中直接使用 python 命令运行 Python 代码了。

![1579587121571](environment-variable.assets\1579587121571.png)