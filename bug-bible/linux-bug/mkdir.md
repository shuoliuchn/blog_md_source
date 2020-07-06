# mkdir

## cannot create directory

### 错误1.1

报错信息：

```shell
mkdir: cannot create directory ‘/tmp/oldboy/python/s26’: No such file or directory
```

错误代码：

```shell
[root@localhost python]# mkdir /tmp/oldboy/python/s26
```

错误原因：

使用 mkdir 命令迭代创建目录时，需要加上 -p 参数，否则会报错。

解决方法：

给 mkdir 加上 -p 参数。

```shell
[root@localhost python]# mkdir-p /tmp/oldboy/python/s26
```