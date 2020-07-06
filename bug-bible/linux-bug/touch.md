# touch

## missing file operand

### 错误1.1

报错信息：

```shell
touch: missing file operand
Try 'touch --help' for more information.
```

错误代码：

```shell
[root@localhost py]# touch >>test1.py<<EOF
> #!coding:utf8
> print('Life is short, I use Python!')
> EOF
```

错误原因：

命令记错了，这应该是文件创建好之后，使用 cat 命令像文件中导入数据的操作。touch 命令没有这个功能。

解决方法：

先使用 touch 命令创建文件，然后使用 cat 命令把代码传进去。

```shell
[root@localhost py]# touch test1.py
[root@localhost py]# cat >>test1.py<<EOF
> #!coding:utf8
> print('Life is short, I use Python!')
> EOF
[root@localhost py]# python test1.py 
```

也可以直接使用 vi 或者 vim 创建并写入代码。