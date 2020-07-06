# bash

## command not found

### 错误1.1

报错信息：

```shell
-bash: nginx: command not found
```

错误代码：

```shell
nginx
```

错误原因：

命令没有错，但是没有配环境变量

解决方法：

去配置环境变量

### 错误1.2

报错信息：

```shell
-bash: vim: command not found
```

错误代码：

```shell
vim
```

错误原因：

没有安装 vim

解决方法：

安装vim

```shell
yum install vim -y
```

