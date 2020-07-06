# yum

## There are no enabled repos

### 错误1.1

报错信息：

```shell
There are no enabled repos.
 Run "yum repolist all" to see the repos you have.
 To enable Red Hat Subscription Management repositories:
     subscription-manager repos --enable <repo>
 To enable custom repositories:
     yum-config-manager --enable <repo>
```

错误代码：

```shell
yum install nginx
```

错误原因：

代码没有错，也没有什么不合适的。只是因为把仓库文件全都转移走了。

解决方法：

把仓库文件弄回来，可以使用阿里云镜像 `mirrors.aliyun.com`