## Git 配置文件详解

配置文件其实分三个：项目配置文件、全局配置文件和系统配置文件。接下来我们将逐一进行讨论。

#### 项目配置文件

只有当前的 git 项目生效， 在项目的 `.git/config` 文件中，对应的配置指令：

```bash
git config --local user.name 'Shuo Liu'
git config --local user.email 'liushuo432@outlook.com'
```

#### 全局配置文件

所有 git 管理的项目都生效。macOS 电脑在 `~/.gitconfig` 中，`Windows` 在：`C:\Users\用户名\.gitconfig`，对应配置指令：

```bash
git config --global user.name 'Shuo Liu'
git config --global user.name 'liushuo432@outlook.com'
```

#### 系统配置文件

所有项目都生效，mac在/etc/.gitconfig文件中，windows在Git 的安装目录下 ，对应配置指令：

```bash
git config --system user.name 'Shuo Liu'
git config --system user.name 'liushuo432@outlook.com'
# 注意：需要管理员权限，也就是root权限
```

应用场景：

```bash
# 配置用户名和邮箱
git config --local user.name 'Shuo Liu'
git config --local user.email 'liushuo432@outlook.com'

# 配置beyond compare工具
git config --local merge.tool bc3
git config --local mergetool.path '/usr/local/bin/bcomp'  # 工具路径
git config --local mergetool.keepBackup false

# 配置push的时候的远程仓库的地址
git remote add origin 地址    # 默认是添加在了本地配置文件中（--local）
```

使用配置项的顺序：本地配置-->全局配置-->系统配置。 

更多的配置相关内容和指令：

配置 Git 的相关参数。

Git 一共有 3 个配置文件：

1. 仓库级的配置文件：在仓库的 `.git/.gitconfig`，该配置文件只对所在的仓库有效。
2. 全局配置文件：Mac 系统在 `~/.gitconfig`，Windows 系统在 `C:\Users\<用户名>\.gitconfig`。
3. 系统级的配置文件：在 Git 的安装目录下（Mac 系统下安装目录在 `/usr/local/git`）的 etc 文件夹中的 gitconfig。

可能会用到的指令：

查看配置信息

```bash
--local：仓库级，--global：全局级，--system：系统级
$ git config <--local | --global | --system> -l
```

查看当前生效的配置信息

```bash
$ git config -l
```

编辑配置文件

```bash
--local：仓库级，--global：全局级，--system：系统级
$ git config <--local | --global | --system> -e
```

添加配置项

```bash
--local：仓库级，--global：全局级，--system：系统级
$ git config <--local | --global | --system> --add <name> <value>
```

获取配置项

```bash
$ git config <--local | --global | --system> --get <name>
```

删除配置项

```bash
$ git config <--local | --global | --system> --unset <name>
```

配置提交记录中的用户信息

```bash
$ git config --global user.name <用户名>
$ git config --global user.email <邮箱地址>
```

更改 Git 缓存区的大小

如果提交的内容较大，默认缓存较小，提交会失败。缓存大小单位：B，例如：524288000（500 MB）

```bash
$ git config --global http.postBuffer <缓存大小>
```

调用 `git status/git diff` 命令时以高亮或彩色方式显示改动状态

```bash
$ git config --global color.ui true
```

配置可以缓存密码，默认缓存时间15分钟

```bash
$ git config --global credential.helper cache
```

配置密码的缓存时间

缓存时间单位：秒

```bash
$ git config --global credential.helper 'cache --timeout=<缓存时间>'
```

配置长期存储密码

```bash
$ git config --global credential.helper store
```